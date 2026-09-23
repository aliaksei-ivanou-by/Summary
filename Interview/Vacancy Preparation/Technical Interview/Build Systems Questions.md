# Build Systems Technical Interview Questions and Answers

> Reusable bank for Make, CMake and C++ dependency management. The compilation and linking model itself - translation units, the linker, static versus shared libraries - lives in [C Language Questions](<./C Language Questions.md>) (C-016 to C-018) and [C++ Core Questions](<./C++ Core Questions.md>); a working CMake + GoogleTest project to type before a live-coding round is in [Live Coding Scaffold](<./Live Coding Scaffold.md>).

# Question Index

## Make (BLD-001–BLD-003)

- [BLD-001. How does `make` decide what to rebuild?](#question-bld-001)
- [BLD-002. What does a Makefile rule look like, and what are the traps?](#question-bld-002)
- [BLD-003. Why do hand-written Makefiles miss header changes, and how is that fixed?](#question-bld-003)

## CMake (BLD-004–BLD-008)

- [BLD-004. Make vs CMake - what is the actual relationship?](#question-bld-004)
- [BLD-005. What does target-based CMake mean, and why does it matter?](#question-bld-005)
- [BLD-006. How do you bring in a dependency in CMake?](#question-bld-006)
- [BLD-007. How do you cross-compile with CMake?](#question-bld-007)
- [BLD-008. How do build types and generators work?](#question-bld-008)

## Dependencies and Speed (BLD-009–BLD-010)

- [BLD-009. vcpkg, Conan or the system package manager?](#question-bld-009)
- [BLD-010. A build takes twenty minutes - what do you do?](#question-bld-010)

---

# 1. Make

## Question BLD-001

[↑ Back to question index](#question-index)

### Question BLD-001 — How does `make` decide what to rebuild?

**Short answer**

- By timestamp. A target is rebuilt when it does not exist or when any of its prerequisites is newer than it, applied recursively through the dependency graph.
- That gives incremental builds for free, and it is the entire model - there is no content hashing and no memory of previous builds.
- The consequences follow directly: a clock skew or a touched file causes spurious rebuilds, and a dependency you forgot to declare causes a *missed* rebuild, which is worse because it is silent.

**Details and nuances**

The silent-miss case is the one worth naming, because it produces the "it works after `make clean`" bug that people blame on the compiler. If `main.o` depends on `config.h` and the Makefile never says so, editing the header changes nothing and the object file keeps stale inlined constants.

`make -j` parallelises independent targets, which is free speed and also the point at which an under-declared dependency graph starts failing intermittently rather than silently - two targets race, and which one wins depends on scheduling.

[↑ Back to question index](#question-index)

---

## Question BLD-002

[↑ Back to question index](#question-index)

### Question BLD-002 — What does a Makefile rule look like, and what are the traps?

**Short answer**

- `target: prerequisites` then, on the next line indented **with a tab**, the recipe - a literal tab, not spaces, which is the first thing that bites everyone.
- Each recipe line runs in its own shell, so `cd build` on one line does not affect the next; join them with `&&` and a line continuation.
- Declare targets that are not files as `.PHONY` - otherwise a file named `clean` in the directory makes `make clean` decide there is nothing to do.

**Details and nuances**

```make
CC      := gcc
CFLAGS  := -Wall -Wextra -g
OBJS    := main.o utils.o

.PHONY: all clean
all: program

program: $(OBJS)
	$(CC) $(CFLAGS) -o $@ $^      # $@ = target, $^ = all prerequisites

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@   # $< = first prerequisite

clean:
	rm -f $(OBJS) program
```

Two more worth knowing: `:=` expands immediately while `=` is re-expanded on every use, which turns an innocent-looking variable into a surprise; and `$$` is needed to pass a `$` through to the shell, because `make` consumes the first one.

**Example or evidence boundary**

Prepared knowledge for hand-written Makefiles. My own projects use CMake, which generates them.

[↑ Back to question index](#question-index)

---

## Question BLD-003

[↑ Back to question index](#question-index)

### Question BLD-003 — Why do hand-written Makefiles miss header changes, and how is that fixed?

**Short answer**

- Because the rule `%.o: %.c` lists only the `.c` file, so `make` has no idea which headers that translation unit included - and the include graph is discovered by the preprocessor, not by the Makefile author.
- The fix is to have the compiler emit the dependency list: `-MMD -MP` writes a `.d` file per object, and the Makefile includes those files.
- This is one of the main reasons projects move to CMake: it does this automatically, and a build system that silently under-rebuilds is worse than a slow one.

**Details and nuances**

```make
CFLAGS += -MMD -MP
-include $(OBJS:.o=.d)      # leading '-' so a missing .d on the first build is not an error
```

`-MMD` writes dependencies for user headers and skips system ones; `-MP` adds a phony target for each header so that deleting a header does not break the build with "no rule to make target".

The honest summary for an interview is that Make's model is fine and its ergonomics are not: everything above is standard practice that every project reinvents, which is the argument for a generator rather than for writing it again.

[↑ Back to question index](#question-index)

---

# 2. CMake

## Question BLD-004

[↑ Back to question index](#question-index)

### Question BLD-004 — Make vs CMake - what is the actual relationship?

**Short answer**

- They are not alternatives at the same level: CMake is a build-system *generator* that produces Makefiles, Ninja files, Visual Studio solutions or Xcode projects from one description.
- So `cmake` configures and `make` or `ninja` builds; `cmake --build build` runs whichever one was generated, which is the portable way to say it.
- Make is enough for a small single-platform project. CMake earns its complexity as soon as there is a second platform, a second compiler, an IDE, or a third-party dependency.

**Details and nuances**

The two-step nature is where confusion starts: configure time evaluates `CMakeLists.txt` and produces the build files, build time runs them. A variable set at configure time is baked in, which is why changing a toolchain usually means deleting the build directory rather than re-running `cmake` over it.

Always build out of source - `cmake -S . -B build` - so the tree stays clean and deleting `build/` is a full clean.

**Example or evidence boundary**

Production experience: CMake on my own C++20 application with CTest integration, and in the project toolchains at RIFTEK and on the embedded platform.

[↑ Back to question index](#question-index)

---

## Question BLD-005

[↑ Back to question index](#question-index)

### Question BLD-005 — What does target-based CMake mean, and why does it matter?

**Short answer**

- Modern CMake attaches every property to a target - include directories, compile features, definitions, link libraries - instead of setting global variables such as `include_directories()` or `CMAKE_CXX_FLAGS`.
- The keywords are the point: `PRIVATE` means the requirement applies when building this target, `INTERFACE` means it applies to consumers, `PUBLIC` means both.
- The payoff is that a consumer just writes `target_link_libraries(app PRIVATE mylib)` and inherits the include paths and flags automatically, instead of repeating them and drifting.

**Details and nuances**

```cmake
add_library(core STATIC src/core.cpp)
target_include_directories(core PUBLIC  include)   # consumers need these headers
target_include_directories(core PRIVATE src)       # internal layout, not exported
target_compile_features(core   PUBLIC  cxx_std_17) # consumers need C++17 too
target_link_libraries(core     PRIVATE fmt::fmt)   # an implementation detail

add_executable(app main.cpp)
target_link_libraries(app PRIVATE core)            # inherits the public parts
```

The old style breaks in a specific way worth describing: global `include_directories()` applies to everything in the directory and below, so one library's headers become visible to targets that never asked for them, and a name collision surfaces as a file being included from the wrong place. Target-based CMake makes the dependency graph explicit - what a target can see is exactly what it declared.

An `INTERFACE` library with no sources is the idiom for a header-only library, and for grouping settings that several targets share.

[↑ Back to question index](#question-index)

---

## Question BLD-006

[↑ Back to question index](#question-index)

### Question BLD-006 — How do you bring in a dependency in CMake?

**Short answer**

- `find_package(Foo REQUIRED)` when it is already installed - the dependency comes from the system, a package manager or a toolchain file, and CMake gives you imported targets like `Foo::Foo`.
- `FetchContent` when you want the build to download and build it itself - reproducible from a clean checkout, at the cost of building it.
- `add_subdirectory` for source you vendor in the repository, typically a git submodule.

**Details and nuances**

```cmake
include(FetchContent)
FetchContent_Declare(googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG        v1.14.0)          # pin a tag, never a branch
FetchContent_MakeAvailable(googletest)
target_link_libraries(tests PRIVATE GTest::gtest_main)
```

The trade-off to state: `FetchContent` needs network access at configure time, which fails in an offline CI runner or a locked-down interview machine - so a project that depends on it should have a fallback, either a vendored copy or a `find_package` path.

Pinning matters for the same reason lockfiles matter elsewhere: `GIT_TAG main` makes the build non-reproducible and turns someone else's commit into your broken build.

[↑ Back to question index](#question-index)

---

## Question BLD-007

[↑ Back to question index](#question-index)

### Question BLD-007 — How do you cross-compile with CMake?

**Short answer**

- With a toolchain file passed at configure time: `cmake -S . -B build -DCMAKE_TOOLCHAIN_FILE=arm.cmake`, which names the target system, the cross compiler and the sysroot.
- Setting `CMAKE_SYSTEM_NAME` is what tells CMake it is cross-compiling, which changes how it searches: headers and libraries come from the sysroot, not from the host.
- Anything that has to run during the build - a code generator, a test - cannot be a cross-compiled binary, so those are built separately for the host.

**Details and nuances**

```cmake
set(CMAKE_SYSTEM_NAME      Linux)
set(CMAKE_SYSTEM_PROCESSOR aarch64)
set(CMAKE_C_COMPILER   aarch64-linux-gnu-gcc)
set(CMAKE_CXX_COMPILER aarch64-linux-gnu-g++)
set(CMAKE_SYSROOT      /opt/sysroots/aarch64)
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)   # tools from the host
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)    # libraries from the sysroot
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
```

Those three `FIND_ROOT_PATH_MODE` lines are the ones that cause real confusion when wrong: without them `find_package` happily finds the host's library and the link fails with an architecture mismatch that looks like a compiler problem.

In a Yocto workflow this file is generated for you - the SDK's environment script sets it up - which is the practical form of the same thing.

**Example or evidence boundary**

Production experience: Yocto/BitBake customisation and target-side deployment and debugging on NXP i.MX. Writing a toolchain file from scratch is prepared knowledge.

[↑ Back to question index](#question-index)

---

## Question BLD-008

[↑ Back to question index](#question-index)

### Question BLD-008 — How do build types and generators work?

**Short answer**

- `CMAKE_BUILD_TYPE` - `Debug`, `Release`, `RelWithDebInfo`, `MinSizeRel` - selects optimisation and debug-symbol flags, and it applies to single-configuration generators such as Makefiles and Ninja.
- Multi-configuration generators, notably Visual Studio and Ninja Multi-Config, ignore it and take the configuration at build time: `cmake --build build --config Release`.
- `RelWithDebInfo` is the one worth knowing by name: optimised, with symbols, which is what you ship when you want a usable core dump from production.

**Details and nuances**

The behaviour difference between generator kinds is a real source of confusion when a project is developed on Linux and built on Windows: `-DCMAKE_BUILD_TYPE=Debug` is silently ignored by the Visual Studio generator, and the developer concludes CMake is broken.

`ninja` is usually the generator to pick for speed - `-G Ninja` - because its dependency handling and parallelism are better than Make's, and it is what CMake's own tooling assumes.

Do not set optimisation flags by hand in `CMAKE_CXX_FLAGS`; they end up fighting the build type. Use the build type, and `target_compile_options` with a generator expression when something genuinely differs per configuration.

[↑ Back to question index](#question-index)

---

# 3. Dependencies and Speed

## Question BLD-009

[↑ Back to question index](#question-index)

### Question BLD-009 — vcpkg, Conan or the system package manager?

**Short answer**

- The system package manager is simplest where it has what you need at the version you need, and useless when it does not - which on an older distribution is most of the time.
- vcpkg builds from source against your toolchain and integrates through a CMake toolchain file; with a manifest it pins versions per project.
- Conan is more configurable and handles binary packages, multiple ABIs and private registries better, at the cost of more concepts to learn.

**Details and nuances**

The question underneath is ABI compatibility, and it is why "just use the system package" fails on C++: a library built with a different compiler, standard-library version or `_GLIBCXX_USE_CXX11_ABI` setting will link and then misbehave. Source-based managers exist to make everything build the same way.

Whatever the choice, the requirements are the same and worth stating as such: pinned versions, reproducible from a clean checkout, and a cache so CI is not rebuilding the world on every run.

[↑ Back to question index](#question-index)

---

## Question BLD-010

[↑ Back to question index](#question-index)

### Question BLD-010 — A build takes twenty minutes - what do you do?

**Short answer**

- Measure before changing anything: which targets, and is it compiling or linking? `ninja -t graph`, `-ftime-trace` with Clang, or simply timing a single translation unit tells you which, and the answers are different.
- Compile-bound is usually include bloat - a widely included header pulling in the world. Forward-declare, move implementation includes into `.cpp` files, apply PImpl at the worst boundaries, and consider precompiled headers or unity builds.
- Then parallelism and caching: `ninja` over `make`, `-j` matched to the machine, and `ccache` so unchanged translation units are not recompiled at all.

**Details and nuances**

The structural answer, if the codebase allows it, is that build time is a dependency-graph problem: if editing one header rebuilds four hundred files, the fix is the graph, not the compiler flags. That is the same diagnosis as [BLD-003](#question-bld-003) from the opposite direction - there, missing edges caused under-building; here, unnecessary edges cause over-building.

Link time is its own case and responds to different treatment: a faster linker such as `lld` or `mold`, fewer and larger static libraries, and dropping link-time optimisation from developer builds while keeping it for release.

Worth saying explicitly, because it is the senior part of the answer: a twenty-minute build is a productivity problem with a cost you can estimate - developers times rebuilds times minutes - which is what justifies spending a week on it.

[↑ Back to question index](#question-index)
