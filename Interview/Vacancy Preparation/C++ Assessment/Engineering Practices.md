# C++ Engineering Practices Assessment — Answer Guide

This handbook covers the engineering topics surrounding production C++: build systems, dependency management, testing, Linux operations, and native-library diagnostics. Every question has a model answer. Tool syntax is useful, but strong answers explain the model, evidence, failure modes, and tradeoffs behind the command.

Labels:

- **[Basic]** — expected working knowledge.
- **[Deep dive]** — non-obvious behavior or tradeoffs.
- **[Code]** — configuration, command, or diagnosis exercise.
- **[Design]** — engineering-system choice.

## Contents

1. [Build systems and dependencies](#1-build-systems-and-dependencies)
   - [Make and incremental rebuilding](#11-make-and-incremental-rebuilding)
   - [Modern target-based CMake](#12-modern-target-based-cmake)
   - [Dependencies, reproducibility, and build speed](#13-dependencies-reproducibility-and-build-speed)
2. [Testing C++ systems](#2-testing-c-systems)
   - [Testing theory and levels](#21-testing-theory-and-levels)
   - [C++ frameworks, fixtures, and test doubles](#22-c-frameworks-fixtures-and-test-doubles)
   - [Concurrency, time, hardware, and legacy code](#23-concurrency-time-hardware-and-legacy-code)
3. [Linux and production operations](#3-linux-and-production-operations)
   - [Filesystem, permissions, and discovery](#31-filesystem-permissions-and-discovery)
   - [Processes, signals, hangs, and services](#32-processes-signals-hangs-and-services)
   - [Shell pipelines and production diagnosis](#33-shell-pipelines-and-production-diagnosis)
4. [Native libraries and plug-ins](#4-native-libraries-and-plug-ins)

---

# 1. Build systems and dependencies

## 1.1. Make and incremental rebuilding

1. **[Basic] How does `make` decide whether to rebuild a target?**

   **Answer.** A rule declares a target, its prerequisites, and a recipe. Traditionally, if the target is missing or any prerequisite has a newer modification time, Make runs the recipe; it recursively updates prerequisites first. This is timestamp/dependency-graph reasoning, not source-code understanding. Incorrect clocks, undeclared generated inputs, or recipes that change behavior based on untracked environment variables can therefore produce stale or unnecessary builds.

2. **[Code] What does a basic Make rule look like, and why must the recipe indentation be correct?**

   **Answer.** A rule has `target: prerequisites` followed by recipe lines. Traditional Make syntax requires a tab before each recipe command; spaces may yield “missing separator.” Automatic variables reduce duplication: `$@` is the target, `$<` the first prerequisite, and `$^` all prerequisites. Each logical recipe line may run in a separate shell unless grouped, so directory changes and shell variables may not persist across lines.

   ```make
   build/widget.o: src/widget.cpp | build
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -MMD -MP -c $< -o $@

   -include build/widget.d
   ```

3. **[Deep dive] Why do hand-written Makefiles often miss header changes?**

   **Answer.** If an object rule names only its `.cpp` input, Make does not know which transitively included headers affect it. Compilers can emit dependency files while compiling, for example GCC/Clang `-MMD -MP`, and the Makefile includes those `.d` files. Generated headers, compiler flags, code generators, and configuration files also need modeled dependencies. Overly broad dependencies make every edit rebuild everything; missing dependencies create incorrect binaries.

4. **[Deep dive] What are phony targets and order-only prerequisites?**

   **Answer.** A `.PHONY` target represents an action such as `clean` rather than a real file, preventing a same-named file from making it appear up to date. An order-only prerequisite (`target: normal | order_only`) must exist or be built first, but its timestamp does not force the target to rebuild. Build directories are a typical order-only prerequisite: touching the directory because another file was created should not recompile every object.

5. **[Design] When should a project use Make directly versus a build-system generator?**

   **Answer.** Direct Make can be transparent and effective for small Unix-specific projects with a controlled toolchain. Cross-platform projects, multiple IDEs/configurations, dependency export/install rules, feature detection, and large target graphs usually benefit from CMake, Meson, Bazel, or another higher-level system. CMake may generate Makefiles or Ninja files; replacing the generator does not replace the lower-level build executor's role.

## 1.2. Modern target-based CMake

1. **[Basic] What is the relationship between CMake and tools such as Make or Ninja?**

   **Answer.** CMake reads project configuration and generates a build graph for a selected generator, such as Ninja, Unix Makefiles, Visual Studio, or Xcode. The generated build tool performs incremental compilation/linking. The usual workflow is configure, build, and optionally test/install. CMake is not a compiler and a `CMakeLists.txt` is not a portable shell script; behavior is expressed through targets and properties.

2. **[Basic] What does target-based CMake mean?**

   **Answer.** Create logical targets with `add_library`/`add_executable`, then attach requirements through `target_sources`, `target_include_directories`, `target_compile_features`, `target_compile_definitions`, and `target_link_libraries`. Consumers inherit only requirements marked `PUBLIC` or `INTERFACE`; `PRIVATE` requirements stay internal. This models dependency propagation and avoids directory-global flags that accidentally affect unrelated code.

3. **[Deep dive] Explain `PRIVATE`, `PUBLIC`, and `INTERFACE`.**

   **Answer.** `PRIVATE` applies when building the target but is not propagated. `INTERFACE` is a usage requirement for consumers and does not apply to compiling the target's own sources. `PUBLIC` does both. If a public header includes a dependency's header or exposes its compile definition, that dependency is normally public/interface; if only a `.cpp` uses it, it is private. Correct visibility makes exported packages usable and limits rebuild/coupling.

4. **[Code] What is a minimal modern target setup?**

   **Answer.** Specify a suitable minimum CMake version/project, create targets, and express usage requirements on those targets. Let CMake select the platform-specific compiler flag for the language feature rather than hard-coding `-std=` globally.

   ```cmake
   cmake_minimum_required(VERSION 3.24)
   project(example LANGUAGES CXX)

   add_library(core src/core.cpp)
   target_include_directories(core PUBLIC
       $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
       $<INSTALL_INTERFACE:include>)
   target_compile_features(core PUBLIC cxx_std_20)

   add_executable(app src/main.cpp)
   target_link_libraries(app PRIVATE core)
   ```

5. **[Basic] How do single-config and multi-config generators differ?**

   **Answer.** Single-config generators such as common Ninja/Make setups choose one configuration at configure time, often via `CMAKE_BUILD_TYPE`. Multi-config generators such as Visual Studio, Xcode, and Ninja Multi-Config generate several configurations and choose one at build time with `--config`. Code that assumes `CMAKE_BUILD_TYPE` always exists breaks under multi-config generators; use configuration-aware target properties and generator expressions when needed.

6. **[Deep dive] How does CMake cross-compilation work?**

   **Answer.** A toolchain file selects the target system, compilers, sysroot, search modes, and related platform facts before the first project configuration. The build machine and target machine differ, so test executables may not be runnable during configure unless an emulator is configured. Host tools such as code generators may need a separate native build. Keep platform tests and dependency discovery target-aware; clearing a cache after changing toolchains avoids stale results.

7. **[Design] How should reusable CMake libraries be exported?**

   **Answer.** Install targets and headers, export a namespaced target set, and provide package configuration/version files so consumers use `find_package` and link an imported target carrying its usage requirements. Avoid exposing build-tree absolute paths or relying on caller-global variables. Decide static/shared variants, transitive dependencies, components, version compatibility, and relocatability. Test consumption from a separate minimal project, not only as a subdirectory.

8. **[Deep dive] What are generator expressions for?**

   **Answer.** Expressions such as `$<CONFIG:Debug>`, `$<BUILD_INTERFACE:...>`, and `$<TARGET_FILE:...>` are evaluated while generating or building for a particular target/configuration. They express context-dependent properties without imperative global branching. They are powerful but difficult to debug when deeply nested; prefer ordinary target properties and named helper functions until configuration-dependent behavior is actually necessary.

## 1.3. Dependencies, reproducibility, and build speed

1. **[Design] How can a CMake project bring in third-party dependencies?**

   **Answer.** Common methods are system/package-manager discovery with `find_package`, a package manager such as Conan or vcpkg, vendored source with `add_subdirectory`, or CMake `FetchContent`. Prefer imported targets over raw include/library variables. Pin versions/checksums for reproducibility, document offline/proxy behavior, and avoid downloading arbitrary mutable content during every configure. The best choice depends on platform policy, binary compatibility, security review, and whether dependencies must be patched.

2. **[Deep dive] What should a dependency lock capture?**

   **Answer.** It should identify source/binary versions and revisions plus relevant options, target platform, compiler/runtime ABI, build type, and transitive resolution. A source version alone does not make a C++ binary compatible across standard libraries, CRT modes, architecture, sanitizer options, or debug iterators. Preserve checksums/provenance and make updates deliberate so builds are repeatable and supply-chain changes are reviewable.

3. **[Basic] What makes a build reproducible?**

   **Answer.** Given the declared sources, dependencies, toolchain, configuration, and controlled environment, it produces functionally identical—and ideally bit-for-bit identical—artifacts. Avoid embedding current timestamps, random paths, usernames, and nondeterministic archive/member order; map source paths when supported and pin tools. Reproducibility improves debugging and supply-chain verification, but it requires all relevant inputs to be declared rather than relying on workstation state.

4. **[Design] A C++ build takes twenty minutes. How do you approach it?**

   **Answer.** Measure configuration, compilation, linking, and code generation separately; inspect build traces and the critical path rather than guessing. Check accidental rebuilds and dependency fan-out, enable safe parallelism and a fast executor, use compiler caches, reduce unnecessary header inclusion/template instantiation, and consider precompiled headers or unity builds with awareness of their tradeoffs. Faster hardware or distributed compilation helps only after the graph is correct. Re-measure clean and incremental developer workflows.

5. **[Deep dive] How do headers affect build scalability?**

   **Answer.** Textual inclusion repeats parsing in every translation unit, and changing a widely included header invalidates many units. Forward declarations, narrow headers, PImpl, moving non-template implementation to `.cpp`, and explicit template instantiation reduce fan-out. Precompiled headers cache a stable common prefix; C++20 modules aim to replace repeated textual parsing but require toolchain/build-system maturity and careful migration. Include-what-you-use correctness and compile-time cost must be balanced.

6. **[Deep dive] What are the benefits and risks of unity/jumbo builds?**

   **Answer.** Combining several source files into one translation unit reduces repeated header parsing and can improve optimization. It can also expose collisions in anonymous namespaces/macros, change initialization or overload visibility, increase peak compiler memory, reduce parallelism, and hide missing includes because another file included them first. Keep ordinary per-file builds in CI and use unity as an optional acceleration, not a correctness crutch.

7. **[Basic] What does a compiler cache do, and when does it miss?**

   **Answer.** Tools such as ccache/sccache reuse compilation results when the effective compiler invocation and all relevant inputs match. Changes to preprocessed headers, flags, compiler version, environment-dependent macros, or paths can cause misses. Track hit rates and reasons; a cache cannot compensate for a dependency graph that recompiles everything, and remote caches need access control and artifact-integrity protections.

---

# 2. Testing C++ systems

## 2.1. Testing theory and levels

1. **[Basic] What is the difference between verification and validation?**

   **Answer.** Verification asks whether the system was built according to its specified design and requirements—“did we build it right?” Validation asks whether it solves the user's real problem—“did we build the right thing?” Reviews, static analysis, and tests can verify stated contracts; user research, acceptance criteria, and production feedback help validate usefulness. A perfectly implemented wrong requirement passes verification but fails validation.

2. **[Basic] Distinguish unit, integration, system, and regression tests.**

   **Answer.** A unit test exercises a small behavior in isolation from slow/uncontrolled dependencies. An integration test checks collaboration across real boundaries such as database, filesystem, process, or library. A system/end-to-end test exercises the deployed product through a public interface. A regression test is any test retained to prevent a previously found defect from returning; it can exist at any level. Names matter less than scope, dependencies, speed, and failure diagnosis.

3. **[Basic] What are black-box, white-box, and gray-box testing?**

   **Answer.** Black-box tests derive cases from public behavior without relying on internals. White-box tests use knowledge of paths, branches, or representation to achieve structural coverage. Gray-box testing uses limited internal knowledge—for example seeding a database while calling a public API. A healthy suite combines contract-focused tests with enough structural insight to cover risky logic without locking every implementation detail.

4. **[Design] What is the test pyramid?**

   **Answer.** It recommends many fast deterministic unit/component tests, fewer integration tests, and a small number of slow broad end-to-end tests. The exact shape varies, but an inverted pyramid produces slow feedback, flaky environmental failures, and poor localization. Broad tests remain essential for wiring and deployment paths that isolated tests cannot prove; the goal is confidence per unit of maintenance/time, not maximizing one layer.

5. **[Deep dive] What makes an automated test good?**

   **Answer.** It checks one meaningful behavior through a stable contract, is deterministic and isolated, fails for a clear reason, runs fast enough for its feedback tier, and has readable arrange/act/assert structure. It controls time, randomness, external state, and concurrency. A test can be worse than none when it passes without asserting the result, depends on sleeps/global order, reproduces the implementation rather than the requirement, or is so brittle that failures are routinely ignored.

6. **[Deep dive] What does code coverage tell you—and not tell you?**

   **Answer.** Statement/branch/condition coverage identifies code that tests did not execute and can reveal obvious gaps. High coverage does not prove meaningful assertions, correct requirements, boundary cases, absence of races, or fault handling. Use coverage as diagnostic evidence and set context-aware expectations, not a universal quality score. Mutation testing can reveal tests that execute code but do not detect plausible behavioral changes.

7. **[Design] How should a bug fix be tested?**

   **Answer.** First create the smallest test that fails for the reported reason, preferably at the lowest stable public boundary. Fix the cause, verify the test now passes, and add broader integration coverage only if the defect crossed a boundary. Include nearby boundary/property cases so the test guards the rule rather than one literal input. Confirm that the test fails on the old behavior; a test that always passed is not a regression test.

## 2.2. C++ frameworks, fixtures, and test doubles

1. **[Basic] Which C++ test frameworks are common, and how should one be chosen?**

   **Answer.** GoogleTest/GoogleMock, Catch2, and doctest are common; Boost.Test and project-specific frameworks also exist. Compare toolchain/platform support, assertion diagnostics, discovery/runner integration, parameterized/typed tests, mocking needs, compile time, dependency policy, and team familiarity. Framework choice is less important than deterministic design and actionable tests; avoid using framework-specific tricks where a plain value-oriented test is clearer.

2. **[Basic] How do GoogleTest fixtures work?**

   **Answer.** A fixture derives from `::testing::Test`; `SetUp` runs before each test and `TearDown` after it, with a fresh fixture instance per test. Use it for shared setup that improves clarity, not as a hidden state machine. Fatal assertions cannot be used safely in a constructor/destructor because the framework cannot abort the test body/setup as intended; put fallible setup in `SetUp` or helper functions returning an explicit result.

3. **[Basic] How do `ASSERT_*` and `EXPECT_*` differ?**

   **Answer.** A failed `ASSERT_*` is fatal to the current function and returns immediately, so use it when continuing would be unsafe or meaningless. A failed `EXPECT_*` records the failure and continues, allowing several independent properties to be reported. Fatal assertions in non-void helpers or worker threads do not control the calling test as many expect; structure helpers and thread result propagation explicitly.

4. **[Deep dive] What are parameterized and typed tests for?**

   **Answer.** Value-parameterized tests run one behavior over data cases, reducing copy-pasted assertions while keeping each case identifiable. Typed/type-parameterized tests apply a contract to multiple implementations or types, useful for container/adaptor interfaces. Avoid giant tables that hide distinct scenarios; name parameters meaningfully and keep case-specific setup understandable.

5. **[Basic] Distinguish a stub, fake, mock, and spy.**

   **Answer.** A stub returns configured responses. A fake is a lightweight working implementation such as an in-memory repository. A mock is programmed with interaction expectations, while a spy records calls for later assertions. Terminology varies, so describe behavior. Prefer state/output assertions and simple fakes when possible; interaction-heavy mocks couple tests to call order and implementation structure.

6. **[Deep dive] When is mocking appropriate?**

   **Answer.** Mock a boundary when the interaction itself is the contract—sending a command once, committing before publishing, or not calling an expensive dependency after validation fails—or when a real dependency is slow/unavailable. Mock interfaces owned by the test's architectural boundary rather than third-party internals. Over-specifying every call creates fragile tests; use loose defaults sparingly and require expectations only for behavior that matters.

7. **[Code] How should exception and error-result behavior be tested?**

   **Answer.** Assert the error category/type and stable semantic data, not a compiler-dependent full message unless that exact text is public. For exceptions, also verify state invariants and resource counts after failure. Inject faults at each relevant step—allocation, I/O, dependency response—to test basic/strong guarantees. For `expected`/status results, assert that callers cannot accidentally treat failure as a valid value and that context is preserved.

## 2.3. Concurrency, time, hardware, and legacy code

1. **[Design] How do you test multithreaded code without relying on sleeps?**

   **Answer.** Expose synchronization points or inject an executor so the test can coordinate phases with barriers, latches, condition variables, promises, or deterministic scheduling. Assert invariants/results after all threads join and propagate worker exceptions to the test thread. Repeat randomized stress separately and run ThreadSanitizer where supported. A sleep only says “wait at least this long”; it neither proves another thread reached a state nor remains reliable on slow/fast machines.

2. **[Deep dive] Can tests prove that concurrent code has no races?**

   **Answer.** Ordinary tests explore a tiny subset of schedules, so passing does not prove race freedom. Combine a design-level happens-before argument, code review, deterministic schedule manipulation, stress, and dynamic tools such as ThreadSanitizer. Static analyzers/model checkers can help for suitable components. Tests should also cover shutdown, cancellation, full/empty boundaries, and exceptions—not only steady-state throughput.

3. **[Design] How should time-dependent code be tested?**

   **Answer.** Inject a clock/timer scheduler through a narrow interface, use a fake monotonic clock, and advance it explicitly. Separate wall-clock calendar semantics from monotonic deadlines. This makes expiry, retry, backoff, and race boundaries instant and deterministic. Keep a small integration test for the real timer implementation; do not replace every `steady_clock::now()` with global mutable test state.

4. **[Design] How do you test randomness?**

   **Answer.** Inject a random engine or seed and record the seed on failure so a case can be reproduced. Test deterministic transformation properties and statistical behavior separately; do not assert one particular sequence unless the generator algorithm/version is part of the contract. Property-based and fuzz testing explore many inputs, while production should still use an appropriate entropy/security source when required.

5. **[Design] How can embedded or hardware-dependent code be tested?**

   **Answer.** Isolate register/I/O access behind minimal ports, test pure protocol/state logic on the host, use fakes/simulators for failure cases, and add hardware-in-the-loop tests for timing/electrical realities. Cross-compile in CI and test serialization against golden vectors. Simulators cannot prove physical timing, interrupts, DMA/cache coherence, or device quirks, so maintain a smaller controlled hardware suite with clear diagnostics.

6. **[Design] How do you introduce tests into an untested legacy C++ codebase?**

   **Answer.** Add characterization tests around behavior that must not change, starting at available seams such as public APIs or subprocess boundaries. When changing one feature, extract only enough dependency/time/I/O seam to test it; use link seams or adapters temporarily if necessary. Refactor in small verified steps and place new logic in testable components. A full rewrite or mass mocking of private methods usually increases risk without creating trustworthy contracts.

7. **[Basic] How should tests be arranged in CI/CD?**

   **Answer.** Run formatting/static checks and fast unit tests early, then integration/system tests, sanitizers, platform matrices, fuzz/soak tests, packaging, and deployment checks in tiers appropriate to cost. Fail fast on deterministic errors but retain enough artifacts—logs, seeds, dumps, test reports—to diagnose failures. Quarantine must be temporary and visible; silently retrying flaky tests converts failures into noise instead of fixing isolation.

8. **[Deep dive] What is the role of sanitizers in a test matrix?**

   **Answer.** ASan/LSan detect many memory/lifetime/leak issues, UBSan selected undefined behavior, and TSan data races. They require instrumented builds, add overhead, and have platform/combination limitations, so use dedicated configurations with representative tests. They complement rather than replace assertions, static analysis, Valgrind, and production hardening. Preserve symbolization and fail the job on actionable findings.

---

# 3. Linux and production operations

## 3.1. Filesystem, permissions, and discovery

1. **[Basic] What commonly lives under `/etc`, `/var`, `/usr`, `/tmp`, `/proc`, and `/dev`?**

   **Answer.** `/etc` holds host configuration; `/var` variable persistent state such as logs/spools; `/usr` installed userland programs/libraries/data; `/tmp` temporary files subject to cleanup policies; `/proc` exposes process/kernel state through a virtual filesystem; and `/dev` contains device nodes/pseudo-devices. Distribution/container conventions vary. Applications should use platform packaging/runtime conventions rather than hard-coding assumptions without configuration.

2. **[Basic] How do Unix file permissions work?**

   **Answer.** Mode bits define read/write/execute for owner, group, and others, modified by ACLs and special bits. For a directory, read lists names, write creates/removes entries, and execute traverses/looks up names. Deleting a file depends mainly on parent-directory permissions, not the file's write bit. `umask` removes permissions from requested creation modes; it does not directly set the final mode.

3. **[Deep dive] What do setuid, setgid, and the sticky bit mean?**

   **Answer.** Setuid/setgid on an executable can set effective user/group identity when executed, subject to OS/mount/security restrictions; such programs require strict input/environment hygiene. Setgid on a directory commonly makes new entries inherit the directory's group. The sticky bit on a shared writable directory such as `/tmp` restricts removal/renaming to appropriate owners/root. These bits are visible in `ls -l` as `s/S/t/T`.

4. **[Basic] How do hard and symbolic links differ?**

   **Answer.** A hard link is another directory entry for the same inode and generally cannot cross filesystems or link directories; data remains until the final hard link/open reference is gone. A symbolic link stores a pathname and may cross filesystems or dangle. Permissions and replacement races differ when following symlinks, so privileged code must use safe open APIs/flags and avoid check-then-open patterns.

5. **[Code] How do `find`, `grep`, `rg`, and `locate` differ?**

   **Answer.** `find` walks the current filesystem tree and filters by metadata/name, optionally executing actions. `grep` searches file contents; `rg` recursively searches content quickly while respecting common ignore rules. `locate` queries a periodically updated filename database and can be stale or incomplete. Quote shell patterns so the shell does not expand them early, and use NUL-delimited output (`-print0`/compatible consumers) when filenames may contain whitespace/newlines.

6. **[Deep dive] What does `sudo` do, and why should configuration be edited with `visudo`?**

   **Answer.** `sudo` applies policy to run a command with another identity—commonly root—while logging/authenticating according to configuration. It does not “make the shell root” unless a shell is explicitly launched. `visudo` locks and syntax-checks the sudoers configuration before installation, reducing the chance of breaking administrative access. Grant narrow commands/arguments where feasible; environment and wildcard rules can unintentionally broaden privilege.

## 3.2. Processes, signals, hangs, and services

1. **[Basic] How do you inspect what is running on Linux?**

   **Answer.** `ps` provides snapshots, `top`/`htop` live resource views, `/proc/PID` detailed process state, `pgrep` searches processes, and `pstree` shows ancestry. Inspect threads, CPU, resident/virtual memory, state, elapsed time, command line, open files (`lsof` or `/proc/PID/fd`), sockets (`ss`), and cgroup limits as relevant. One high-level metric is a clue, not a root cause.

2. **[Basic] What does `kill` actually do?**

   **Answer.** It sends a signal to a process or process group; it does not inherently terminate it. With no explicit signal, it sends SIGTERM, which can be caught to perform cooperative shutdown. Signal delivery depends on permissions and process state. Shell job specifications and negative/group IDs have distinct meanings, so confirm the exact target before signaling production workloads.

3. **[Deep dive] Why is `kill -9` (SIGKILL) a last resort?**

   **Answer.** SIGKILL cannot be caught, blocked, or handled, so the kernel terminates the process without application cleanup: buffered data may be lost, transactions/locks external to the process may need recovery, and diagnostic handlers cannot run. Use SIGTERM with a bounded grace period first, collect stacks/metrics if a hang needs diagnosis, then escalate when safety/availability requires it. SIGKILL still cannot terminate tasks stuck indefinitely in certain uninterruptible kernel waits until that wait resolves.

4. **[Design] A process is hung. What evidence do you collect?**

   **Answer.** Confirm whether it is CPU-spinning, sleeping, blocked in I/O, deadlocked, or starved. Capture repeated all-thread stacks (`gdb`, `pstack`, core/hang dump), process/thread states, syscall activity (`strace` with care), open sockets/files, queue/lock metrics, CPU/off-CPU profiles, and downstream health. Repeated snapshots distinguish no progress from a slow operation. Collect build/symbol/config IDs and avoid “fixing” it before preserving the decisive evidence.

5. **[Deep dive] What signal-handling restrictions matter in C/C++?**

   **Answer.** An asynchronous signal handler may interrupt code at almost any point. Only async-signal-safe operations are allowed; allocating, locking a normal mutex, using iostreams, and most library calls are unsafe and may deadlock/corrupt state. A robust handler writes minimal data to a pipe/eventfd or sets a `sig_atomic_t`-appropriate flag, with normal code doing complex work. Synchronous faults such as SIGSEGV are not generally recoverable by returning to arbitrary C++ execution.

6. **[Basic] How do you manage a service with systemd?**

   **Answer.** Use `systemctl status/start/stop/restart/reload`, `systemctl enable/disable` for boot policy, and `journalctl -u unit` for logs. A unit declares executable, dependencies/order, restart policy, identity, environment, resource limits, and sandboxing. Prefer readiness notification or a correct service type over arbitrary startup sleeps. After changing unit files, run `systemctl daemon-reload`; distinguish service restart from reloading configuration.

7. **[Deep dive] What makes a good service shutdown sequence?**

   **Answer.** Stop admitting new work, signal cancellation, bound/drain in-flight operations according to deadlines, flush/commit durable state where safe, close listeners and owned resources, join threads/children, and exit with an informative status. Shutdown must be idempotent and have a maximum duration because the supervisor will eventually escalate. Test shutdown during initialization, high load, partial dependency failure, and repeated signals.

8. **[Design] What should a watchdog or health check prove?**

   **Answer.** Liveness says the process/event loop is making progress; readiness says it can currently serve traffic; deeper dependency checks may be diagnostic but can cause cascading removal if coupled incorrectly. A thread that merely updates a timestamp can remain alive while core work is deadlocked, so health should cover meaningful progress. Define thresholds, startup grace, degraded states, and restart rate limits; restart is containment, not root-cause analysis.

## 3.3. Shell pipelines and production diagnosis

1. **[Basic] How do redirection and pipelines work?**

   **Answer.** `>` redirects stdout to a truncated file, `>>` appends, `<` redirects stdin, and `2>` redirects stderr. A pipe connects one process's stdout to another's stdin; by default it does not include stderr. Redirections are processed left-to-right, so `cmd >file 2>&1` differs from `cmd 2>&1 >file`. Pipeline stages run as separate processes/subshell contexts in common shells.

2. **[Deep dive] What does a shebang do?**

   **Answer.** When an executable text file starts with `#!interpreter optional-arg`, the kernel invokes that interpreter with the script path. `#!/bin/bash` selects a known absolute interpreter; `#!/usr/bin/env bash` searches `PATH`, improving portability but inheriting environment/path trust and usually permitting only one argument portably. The script still needs execute permission and compatible line endings.

3. **[Code] What practices make a Bash script safer?**

   **Answer.** Quote expansions (`"$var"`), use arrays for argument lists, validate inputs/paths, prefer `printf`, use `mktemp` and `trap` for cleanup, avoid parsing `ls`, and use NUL-delimited tools for arbitrary filenames. `set -euo pipefail` can catch failures but has contextual exceptions and is not a substitute for explicit checks. Never construct shell code with `eval` from untrusted data; separate data from commands.

4. **[Deep dive] Why can `cmd | while read ...; do value=...; done` lose variable changes?**

   **Answer.** In many shells, pipeline components execute in subshells, so assignments inside the loop do not modify the parent shell. Redirect or use process substitution where appropriate: `while IFS= read -r line; do ...; done < file` or `done < <(cmd)` in Bash. Also use `IFS=` and `read -r` to preserve whitespace/backslashes unless transformation is intended.

5. **[Design] Where do you look when a production service is misbehaving?**

   **Answer.** Start from the user-visible symptom and time window, deployment/config changes, service/supervisor status, structured logs, error/latency/saturation metrics, traces, resource/cgroup limits, queues, dependencies, and host/kernel events. Correlate by request/trace/build ID. Choose deeper tools—stacks, `strace`, `perf`, packet capture, heap profiles—based on a hypothesis. Keep a timeline and distinguish correlation from causation.

6. **[Deep dive] What risks do diagnostic commands introduce in production?**

   **Answer.** Attaching a debugger pauses threads; syscall tracing and verbose logging add overhead; packet captures and dumps contain sensitive data; recursive scans can overload storage; profiling changes timing. Scope by PID/interface/duration/filter, capture to bounded secure storage, coordinate pauses, and know rollback. Prefer pre-built low-overhead telemetry for common questions and rehearse incident procedures before an outage.

7. **[Design] How should incident evidence be preserved?**

   **Answer.** Record exact times/time zone, affected IDs, commands and outputs, build/container digest, configuration, topology, and whether evidence was captured before or after mitigation. Store logs, profiles, dumps, and packet captures with access controls and retention appropriate to their sensitive contents. A concise chronological incident notebook prevents repeated destructive experiments and supports a later root-cause analysis.

---

# 4. Native libraries and plug-ins

1. **[Basic] How are native libraries loaded explicitly on Windows and POSIX systems?**

   **Answer.** Windows uses `LoadLibrary`/`LoadLibraryEx` to obtain an `HMODULE`, `GetProcAddress` for exported symbols, and `FreeLibrary` to release a reference. POSIX uses `dlopen`, `dlsym`, `dlerror`, and `dlclose`. Symbol lookup returns an untyped address that must be converted/used according to platform rules and the exact ABI signature. Loading executes platform loader work and may run initialization code, so paths and trust policy matter.

2. **[Deep dive] Why is complex work in Windows `DllMain` dangerous?**

   **Answer.** `DllMain` runs while the loader lock is held and under strict reentrancy constraints. Loading another DLL, creating/joining threads, acquiring locks that interact with loader activity, COM initialization, or substantial library work can deadlock or observe partially initialized process state. Keep it minimal—typically store the module handle or initialize trivial thread-local state—and expose an explicit initialization function after loading.

3. **[Design] What should an explicit plug-in entry point look like?**

   **Answer.** Prefer a small `extern "C"` exported function with a stable unmangled name that negotiates an ABI version and returns/fills a versioned function table using fixed-width types and opaque handles. Pair allocation/deallocation on the same side, make ownership and threading explicit, and prevent exceptions/STL/compiler-specific layouts from crossing an unknown runtime boundary. Validate table size/version before calling optional functions.

4. **[Design] How do you debug a host-process crash apparently caused by a native plug-in?**

   **Answer.** Preserve a dump and exact host/plug-in binaries, symbols, architecture, configuration, and loaded-module list. Identify the faulting thread/instruction and walk outward: corrupted stacks often make the top frame a victim rather than cause. Enable page heap/ASan or allocator diagnostics in a reproducible environment, verify ABI/calling convention/runtime/ownership agreement, and test load/unload and callback races. Minimize the plug-in request/input while retaining the failure.

5. **[Deep dive] Why can unloading a plug-in be unsafe?**

   **Answer.** Threads, callbacks, virtual objects, function pointers, TLS destructors, queued tasks, or exception metadata may still refer to code/data in the module. After unload those addresses are invalid, producing delayed crashes. Define a quiescence protocol: stop new calls, unregister callbacks, cancel/drain work, destroy all module-owned objects on the correct side, join threads, then unload. Many systems deliberately keep loaded plug-ins for process lifetime because proving quiescence is harder than retaining the mapping.

6. **[Deep dive] How do symbol visibility and export control help?**

   **Answer.** Export only the intended ABI surface using platform visibility attributes/export definitions and hide other symbols by default. This reduces collisions, load/relocation work, accidental dependencies, and compatibility commitments. Inspect the final binary's dynamic/export symbol table in CI. Visibility does not by itself create a stable ABI; layout, runtime, calling convention, ownership, and semantics still need control.

---

# Assessment usage notes

- Use build and test questions to evaluate whether the candidate can make results repeatable for the whole team, not only on one workstation.
- In diagnostic scenarios, ask what evidence each tool provides, its blind spots, and its production risk.
- For senior interviews, connect layers: a missing dependency edge can ship stale code; an ABI mismatch can appear as heap corruption; an unbounded retry can become a production overload.
- Commands are platform/version dependent. Reward a correct investigation model and safe verification over memorized flags.
