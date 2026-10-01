# Git and CI/CD Assessment — Answer Guide

This handbook covers Git collaboration and the continuous-integration/delivery practices expected from production C++ engineers. Every question has a model answer. Commands are examples; strong answers explain the underlying state transition, recovery path, and effect on collaborators.

Labels:

- **[Basic]** — expected daily knowledge.
- **[Deep dive]** — internals, recovery, or non-obvious tradeoffs.
- **[Code]** — command/configuration exercise.
- **[Design]** — workflow or delivery-system choice.

## Contents

1. [Git's object and working-state model](#1-gits-object-and-working-state-model)
2. [Everyday history manipulation and recovery](#2-everyday-history-manipulation-and-recovery)
3. [Collaboration, review, and repository strategy](#3-collaboration-review-and-repository-strategy)
4. [Continuous integration for C++](#4-continuous-integration-for-c)
5. [Continuous delivery, releases, and deployment](#5-continuous-delivery-releases-and-deployment)

---

# 1. Git's object and working-state model

1. **[Basic] What is a Git commit?**

   **Answer.** A commit is an immutable object containing a pointer to a root tree snapshot, zero or more parent commit IDs, author/committer metadata, and a message. Its object ID is derived from its serialized contents, so changing the snapshot, parent, metadata, or message produces a different commit. Git stores snapshots with deduplicated objects rather than a mutable sequence of patches, though history can be displayed as diffs between snapshots.

2. **[Basic] What are blobs, trees, commits, and annotated tags?**

   **Answer.** A blob stores file contents without a filename. A tree maps names and modes to blobs or subtrees, representing a directory. A commit points to a tree and parent commits. An annotated tag is a separate signed/signable object naming another object and carrying a tagger/message; a lightweight tag is only a ref. These content-addressed objects form a directed acyclic graph.

3. **[Basic] Distinguish the working tree, index, and `HEAD`.**

   **Answer.** `HEAD` normally names the currently checked-out branch, whose ref names a commit. The index (staging area) is the proposed next tree and also holds conflict stages during a merge. The working tree contains editable filesystem files. A path may therefore have one version in `HEAD`, another staged in the index, and a third unstaged in the working tree; `git diff`, `git diff --cached`, and `git diff HEAD` compare different pairs.

   ```mermaid
   flowchart LR
       H[HEAD commit] -->|checkout / restore| W[working tree]
       W -->|git add| I[index / next snapshot]
       I -->|git commit| C[new commit]
       C -->|moves branch ref| H
   ```

4. **[Basic] What is a Git branch?**

   **Answer.** A branch is a movable reference to one commit; it is not a separate directory or copy of every file. Creating a branch is cheap. Committing while `HEAD` is attached to that branch advances the reference. In detached-HEAD state, `HEAD` points directly to a commit; new commits remain valid but can become unreachable unless a branch/tag is created before reflog expiry and garbage collection.

5. **[Deep dive] What are local branches, remote-tracking branches, and remotes?**

   **Answer.** A local branch such as `main` is updated by local operations. A remote is a named set of repository URLs and refspecs, commonly `origin`. A remote-tracking ref such as `origin/main` records the remote branch state seen at the last fetch; it is updated by fetch, not by committing locally. An upstream configuration lets `pull`, `push`, and status compare a local branch with a chosen remote-tracking branch.

6. **[Basic] What do `fetch`, `pull`, and `push` do?**

   **Answer.** `fetch` downloads objects and updates configured remote-tracking refs without integrating them into the current branch. `pull` performs a fetch followed by a configured integration step, normally merge or rebase. `push` asks the remote to update refs after transferring missing objects; the remote can reject non-fast-forward or policy-violating updates. Fetch plus an explicit review/integration command is easier to reason about than an implicit pull when history matters.

7. **[Deep dive] What is a fast-forward update?**

   **Answer.** Ref A can fast-forward to commit B when A is an ancestor of B; moving the ref loses no reachable commits and needs no merge commit. A non-fast-forward update replaces one line of history with another and can make commits unreachable from that ref. Servers commonly reject it on protected branches. `--force-with-lease` is safer than unconditional force because it checks that the remote still has the expected old value.

8. **[Deep dive] What does Git mean by “content-addressed,” and what does it not guarantee?**

   **Answer.** Objects are retrieved by a cryptographic hash of their type/size/content, so accidental corruption or content substitution is detectable under Git's object model. This does not authenticate who created a commit or whether its code is trustworthy. Signed commits/tags, protected refs, reviewed changes, trusted CI, and artifact provenance address identity and process; the hash alone is not a security approval.

---

# 2. Everyday history manipulation and recovery

1. **[Basic] How do merge and rebase differ?**

   **Answer.** Merge creates a commit with multiple parents (unless fast-forwarding), preserving the fact that histories diverged. Rebase copies a sequence of commits onto a new base, producing new commit IDs and a linearized history. Rebase is useful for cleaning private work; rewriting a published branch requires coordination and can invalidate reviews/build associations. The final file snapshot may be identical while history and conflict resolution differ.

2. **[Code] How should a merge conflict be resolved?**

   **Answer.** Inspect `git status`, understand the base/ours/theirs changes, edit the working file to the intended combined behavior, run tests, stage the resolved path, and continue or commit the merge/rebase. Do not blindly choose “ours” or “theirs”: their meaning changes by operation and can discard a valid independent change. Use `git diff --base/--ours/--theirs`, `git mergetool`, or conflict-marker styles with the base when helpful; abort is available while the operation is in progress.

3. **[Basic] Compare `reset --soft`, `reset --mixed`, `reset --hard`, and `revert`.**

   **Answer.** `reset --soft <commit>` moves the current branch/HEAD while preserving index and working tree. Default/mixed reset also resets the index to the target but leaves working files. Hard reset also overwrites tracked working files, destroying uncommitted tracked changes. `revert` creates a new commit applying the inverse of an existing commit and is therefore the usual safe undo on shared history. Always inspect exact refs and preserve valuable work before destructive reset.

4. **[Basic] How do `restore`, `switch`, and the older overloaded `checkout` relate?**

   **Answer.** `switch` changes/creates branches; `restore` copies path content from the index or another tree into the working tree/index. `checkout` historically performs both roles, making commands ambiguous. `restore --staged path` unstages by resetting the index entry while normally preserving the working file; `restore path` without care discards unstaged changes. The underlying operation matters more than command age: identify the source tree and destination state.

5. **[Basic] What are `cherry-pick` and `stash` for?**

   **Answer.** Cherry-pick applies the change introduced by selected commit(s) onto the current branch and creates new commit IDs; it is useful for targeted backports but repeated picking can duplicate history and complicate later merges. Stash records working/index changes in commit-like objects and restores a cleaner tree; it is temporary local workflow, not a replacement for meaningful commits. Stash application can conflict, and untracked files require explicit inclusion.

6. **[Deep dive] What is the reflog, and when can it recover work?**

   **Answer.** Reflogs record recent local movements of refs and `HEAD`, including rebases, resets, branch deletion, and detached commits. `git reflog` can reveal an old commit ID, after which a branch can be created or commits cherry-picked. Reflogs are local, expire, and unreachable objects can later be pruned, so they are a recovery window rather than backup. They do not normally recover uncommitted working-tree contents.

7. **[Code] How do you find the commit that introduced a regression?**

   **Answer.** Use `git bisect`: mark a known bad commit and a known good ancestor, then test the midpoint Git selects and mark it good/bad until the first bad commit is isolated. Automate with `git bisect run <script>` when the test gives reliable exit codes. If builds are broken/skipped, mark untestable points appropriately. The result identifies the first commit correlated with the predicate; inspect dependencies, generated artifacts, and test validity before declaring root cause.

8. **[Deep dive] What do `git blame` and history search prove?**

   **Answer.** `blame` identifies the commit that last changed each surviving line in the selected history, not who designed the behavior or introduced the original bug. Moves, refactors, formatting, and copied code can obscure provenance. `git log -S<string>` searches changes in occurrence count; `-G<regex>` searches matching diff lines. Use them to find context and discussions, not to assign personal blame.

9. **[Code] What should happen after a secret is committed?**

   **Answer.** Revoke/rotate the credential immediately; deleting it in a later commit does not remove it from clones, caches, logs, artifacts, or the provider. Assess exposure and audit usage. If policy requires, rewrite all affected refs with a suitable history-filtering tool, coordinate force updates, purge CI/package caches, and tell collaborators to re-clone or clean refs. Add secret scanning and prevent future storage. History rewriting reduces discoverability but cannot make a leaked secret safe again.

10. **[Deep dive] How do interactive rebase and commit amendment affect history?**

    **Answer.** Amend replaces the current commit; interactive rebase can reorder, squash, edit, drop, or reword a commit sequence. Every rewritten commit and its descendants receive new IDs because parent/content/metadata changed. Use these tools to prepare a coherent private series, then push. On a shared branch, coordinate and use guarded force updates because collaborators' commits and references may otherwise be lost.

11. **[Code] How can a change be split into reviewable commits after files were edited together?**

    **Answer.** Use patch staging (`git add -p`), stage one logical change, verify the staged diff, and commit it; repeat for the rest. If a mixed commit already exists privately, reset it while keeping changes and rebuild commits, or use interactive rebase/edit. Each commit should compile/test where practical and explain one intent. Avoid splitting mechanically by file when one logical change spans production code, tests, and build configuration.

---

# 3. Collaboration, review, and repository strategy

1. **[Design] How do trunk-based development, GitHub Flow, and GitFlow differ?**

   **Answer.** Trunk-based development integrates small changes into one main line frequently, using short-lived branches and feature flags. GitHub-style flow uses short branches and pull requests into a deployable main branch. GitFlow adds long-lived develop/release/hotfix branches and fits some scheduled multi-version releases but increases merge/cherry-pick complexity. Choose based on release cadence, supported versions, regulation, team size, and deployment capability—not fashion.

2. **[Design] What should branch protection enforce?**

   **Answer.** Common rules require reviewed pull requests, successful required checks on the exact commit, no unresolved conversations, restricted force-push/deletion, and controlled release/tag permissions. CODEOWNERS or risk-based approvals can protect sensitive areas. Administrators should not routinely bypass the rules; emergency procedures need audit and follow-up. Protection must also secure CI configuration because modifying the pipeline can bypass code controls or expose secrets.

3. **[Design] What makes a change easy to review?**

   **Answer.** Keep it focused, explain problem/approach/risks/testing, separate mechanical refactoring from behavior changes, include relevant tests, and avoid generated/noisy diffs. Reviewers should assess correctness, ownership/lifetime, concurrency, error paths, security, ABI/API compatibility, performance evidence, and operability—not only style. Large work can be staged behind inactive seams/flags while each merged commit remains safe.

4. **[Deep dive] Why should a C++ commit ideally remain buildable?**

   **Answer.** Buildable, tested commits make bisect reliable, simplify reverts/backports, and preserve an understandable evolution. Cross-cutting changes sometimes require an atomic commit, but gratuitous “header now, implementation later” breaks every intermediate state. Use compatibility adapters, additive APIs, or preparatory refactors to sequence large migrations. Generated changes and platform matrices must also remain consistent.

5. **[Basic] When should changes be merged versus squashed?**

   **Answer.** A merge commit preserves branch topology and individual commits, useful when the series is intentionally curated. Squash merge creates one mainline commit and hides fixup noise, useful when branch commits are work-in-progress. Rebase merge preserves individual commits linearly but rewrites them. The repository should choose a predictable policy while allowing exceptions for reviewable multi-commit migrations; commit messages on main must remain useful.

6. **[Deep dive] What problem do signed commits and signed tags solve?**

   **Answer.** A verified signature links an object to a cryptographic identity/key and detects object alteration. It does not prove that the code was reviewed, the developer machine/key was uncompromised, or the build artifact came from that source. Protect keys, define trusted identities, sign release tags/provenance where valuable, and combine signatures with protected refs and a trusted build pipeline.

7. **[Design] Submodule, subtree, package manager, or vendored copy?**

   **Answer.** A submodule pins another repository commit but requires explicit recursive clone/update and separate access; the parent stores only a gitlink. A subtree copies history/content into the repository and is simpler for consumers but harder to sync. A package manager expresses versioned dependencies and can supply binaries/metadata; vendoring maximizes control/offline builds but increases update responsibility. Choose using ownership, patching, reproducibility, source availability, and contributor workflow.

8. **[Deep dive] When is Git LFS appropriate?**

   **Answer.** LFS stores small pointer files in Git while large content lives in a separate object service, reducing ordinary clone/history size for models, media, or binary test assets. It adds server quotas, authentication, availability, and archival concerns; it does not make frequently changing binaries diffable. Build artifacts generally belong in an artifact/package repository rather than source history or LFS.

9. **[Design] Which repository hooks belong on clients versus servers/CI?**

   **Answer.** Client hooks provide fast convenience—formatting hints, generated checks, commit-message validation—but ordinary Git does not distribute/enforce them reliably and users can bypass them. Security and merge requirements must run on the server or required CI. Keep local hooks fast and reproducible, and provide the same commands for manual/CI execution to avoid “works only in the hook” behavior.

10. **[Deep dive] Why can line-ending, filename-case, and executable-bit differences break cross-platform C++ work?**

    **Answer.** Windows and Unix differ in default line endings, case sensitivity, symlink/executable support, and path rules. A repository can accidentally contain case-colliding headers or lose script executability; generated projects can churn line endings. Define `.gitattributes`, consistent formatting, case-safe names, and CI on supported filesystems. Treat file modes and symlinks deliberately rather than relying on each developer's global Git settings.

---

# 4. Continuous integration for C++

1. **[Basic] What is continuous integration?**

   **Answer.** Developers integrate small changes frequently into a shared main line, and an automated system builds and validates each candidate quickly. CI is both a practice and infrastructure: a nightly build without frequent integration and actionable failures is not sufficient. The goal is to detect incompatibility near the change, keep main releasable under the team's policy, and make validation reproducible outside one workstation.

2. **[Basic] Distinguish pipeline, stage, job, step, runner/agent, and workflow artifact.**

   **Answer.** A pipeline/workflow is one execution graph triggered by an event. Jobs are schedulable units run by agents/runners; steps execute within a job. Stages commonly group jobs with dependency/gating order. An artifact is an immutable output uploaded for later jobs or release use. Exact names vary by Jenkins, GitLab CI, GitHub Actions, Azure, and others, so explain dependencies and isolation rather than memorizing vendor vocabulary.

3. **[Design] What should a basic C++ CI pipeline validate?**

   **Answer.** Configure from a clean checkout; build with supported compilers/configurations; run unit/integration tests; collect test reports; run formatting/static analysis as policy requires; and publish binaries/symbols/metadata only after validation. Separate fast pull-request checks from heavier sanitizer, platform, fuzz, packaging, and performance tiers. Failures must retain logs, exact commands, compiler/cache stats, dumps, and reproducible environment information.

   ```mermaid
   flowchart LR
       A[checkout + dependency lock] --> B[configure]
       B --> C[compile matrix]
       C --> D[unit and integration tests]
       D --> E[sanitizers / static analysis]
       E --> F[package + symbols + SBOM]
       F --> G[immutable artifact promotion]
   ```

4. **[Design] Which dimensions belong in a C++ build matrix?**

   **Answer.** Cover supported OS/architecture, compiler and standard-library families, language modes, debug/release or relevant configurations, shared/static linkage, and critical feature flags. Sanitizers often need dedicated compatible entries. Do not take the Cartesian product blindly: choose risk-based representative combinations, run the full support matrix at appropriate cadence, and record the exact toolchain/container/SDK identity.

5. **[Deep dive] How do caches differ from artifacts?**

   **Answer.** A cache is an optimization that may be missing, evicted, or restored approximately by a key; correctness must not depend on it. An artifact is a named output of a run retained for downstream jobs, audit, or release and should have integrity/provenance metadata. Compiler and dependency caches need keys covering compiler, flags, dependency lock, platform, and relevant sources. Restoring an incompatible cache can create subtle C++ ABI or stale-generated-file failures.

6. **[Deep dive] How should CI avoid “works only on the agent” builds?**

   **Answer.** Declare/pin toolchains and dependencies, start from clean/controlled environments, avoid undeclared global SDK paths, and make the same build/test commands runnable locally. Preserve lockfiles/toolchain files and expose environment differences. Containers help but do not pin the host kernel/CPU or replace cross-platform testing. Periodically rebuild without caches and verify produced packages in a separate clean consumer/runtime environment.

7. **[Design] How should sanitizers and static analysis be integrated?**

   **Answer.** Build dedicated ASan/UBSan/TSan configurations with compatible compiler flags and representative tests; preserve symbolized reports and treat new actionable findings as failures. Run static analyzers with a compilation database and versioned configuration, baseline existing debt explicitly, and block newly introduced high-confidence issues. Sanitizers execute paths and static tools approximate code; neither proves correctness, so combine them with review and tests.

8. **[Deep dive] How should flaky tests be handled?**

   **Answer.** Record the failure and environment, make seeds/schedules visible, and fix isolation, time, resource, or ordering assumptions. Automatic retry may classify instability but must not silently turn red into green; report both attempts and track a flake budget/owner. Quarantine is a time-bounded last resort with visible coverage loss. For C++, investigate races, dangling lifetimes, port/file collisions, timeouts, and tests that depend on unspecified iteration order.

9. **[Design] What should a quality gate block?**

   **Answer.** Block objectively actionable conditions tied to project policy: compile/test failure, required review missing, security/license violation, incompatible API/ABI change, packaging failure, or regression beyond an agreed performance tolerance. Coverage percentages, warning counts, and style metrics are signals but crude universal gates create gaming. Baseline legacy debt and enforce no-regression while deliberately paying it down.

10. **[Deep dive] How should untrusted pull requests be handled?**

    **Answer.** A pull request can modify build scripts/tests and execute arbitrary code on a runner. Do not expose deployment credentials or privileged persistent runners to untrusted forks; use isolated ephemeral workers, minimal permissions, read-only tokens, network restrictions, and separate trusted approval workflows for privileged operations. Cache poisoning and artifact substitution also require scoped keys, integrity checks, and trust-boundary separation.

11. **[Design] What is software-supply-chain provenance?**

    **Answer.** It records which source commit, dependencies, toolchain, builder identity, parameters, and steps produced an artifact, ideally in a signed verifiable attestation. Pair it with hashes/signatures, an SBOM, protected build definitions, dependency verification, and controlled artifact storage. Provenance supports audit and incident response; it does not guarantee the source/dependencies are vulnerability-free.

12. **[Basic] What is an SBOM, and why is it useful for native C++?**

    **Answer.** A Software Bill of Materials inventories components and versions included in a product. Native binaries can statically embed libraries that no runtime package manager reports, so mapping source/package versions into the final artifact is important. Use the SBOM to answer exposure and licensing questions, then verify reachability/configuration because a version match alone does not prove exploitability. Generate it from the resolved build, not a manually maintained guess.

13. **[Deep dive] How should CI treat performance benchmarks?**

    **Answer.** Run correctness-preserving representative benchmarks on controlled hardware or statistically modeled environments; record distributions, noise, compiler/CPU/configuration, and baseline confidence. Avoid blocking every change on tiny noisy microbenchmark differences. Use thresholds for meaningful regressions, scheduled trend runs, and manual investigation, while keeping end-to-end latency/throughput tests distinct from microbenchmarks.

14. **[Design] How can pipeline time be reduced safely?**

    **Answer.** Measure queue and critical-path time, parallelize independent jobs, use correct compiler/dependency caches, shard tests by measured duration, cancel superseded runs, and run risk-based tiers. Reduce redundant configurations while preserving the support matrix. Never share mutable build directories between concurrent jobs or skip clean/cache-miss validation; speed that creates nondeterminism increases total feedback time.

---

# 5. Continuous delivery, releases, and deployment

1. **[Basic] Distinguish continuous delivery from continuous deployment.**

   **Answer.** Continuous delivery keeps every validated change deployable and automates the path to production, usually with a business/manual approval decision. Continuous deployment automatically releases every qualifying change. Both require reliable automated validation, immutable artifacts, environment/configuration management, observability, and recovery. A scheduled manual copy of locally built binaries is neither.

2. **[Design] Why should a release promote the same artifact instead of rebuilding it per environment?**

   **Answer.** Promotion ensures the bits tested in CI are the bits deployed. Rebuilding can resolve different dependencies, timestamps, generators, or toolchains and creates an unvalidated artifact. Store immutable packages, symbols, hashes, SBOM/provenance, and configuration separately; verify signatures/digests at each boundary. Environment-specific secrets and endpoints should not require recompilation unless the platform genuinely demands separate binaries.

3. **[Basic] How should a C++ application be versioned?**

   **Answer.** Expose a product/package version plus build provenance such as commit and toolchain; use a version scheme with documented compatibility meaning. Semantic Versioning can describe public API changes but does not automatically capture C++ ABI compatibility, data/protocol schemas, or independently deployed services. Keep file/package/soname/ABI/protocol versions distinct when their lifecycles differ.

4. **[Design] What belongs in a release artifact set for native software?**

   **Answer.** The executable/libraries or installable package, runtime dependencies or declared requirements, configuration schema/defaults, license notices, migration scripts, debug symbols stored securely, checksums/signatures, SBOM/provenance, and release notes. Validate installation, upgrade, uninstall/rollback, and startup in a clean environment. Symbols should match exact binaries even if distributed through a restricted symbol server rather than to end users.

5. **[Design] Compare rolling, blue-green, and canary deployment.**

   **Answer.** Rolling replacement gradually updates instances in one pool and is resource-efficient but creates a mixed-version period. Blue-green prepares a parallel environment and switches traffic, enabling rapid traffic rollback at higher infrastructure/data-migration cost. Canary sends limited traffic to the new version and expands based on health, limiting blast radius but requiring representative routing and analysis. All require backward-compatible protocols/data during overlap.

6. **[Deep dive] Why is rollback not always safe?**

   **Answer.** A new version may have written an incompatible schema/data format, emitted new events, consumed irreversible inputs, or triggered external side effects. Binary rollback cannot undo those effects. Design expand/migrate/contract database changes, tolerant readers, versioned messages, idempotent operations, and backups/restore tests. Sometimes forward-fixing or disabling a feature is safer than deploying the old binary.

7. **[Design] How do feature flags help delivery, and what problems do they create?**

   **Answer.** Flags separate code deployment from feature exposure, enable gradual cohorts and fast disablement, and let incomplete paths merge safely when the inactive path is harmless. They create combinatorial states, stale code, runtime configuration dependency, and authorization/privacy risks. Give each flag an owner, type, default, telemetry, expiration/removal plan, and test important on/off transitions.

8. **[Deep dive] How should configuration and secrets be delivered?**

   **Answer.** Treat non-secret configuration as validated, versioned operational input with documented defaults; use a secret manager or platform mechanism for credentials, scope access minimally, rotate, and avoid logs/environment exposure where threat models disallow it. Fail startup clearly on invalid required values. Do not bake secrets into images, repositories, artifacts, crash dumps, or command lines visible to other processes.

9. **[Design] What should deployment health gates observe?**

   **Answer.** Compare errors, latency percentiles, saturation, restarts, readiness, business correctness indicators, and dependency behavior between the candidate and baseline over enough traffic/time. Synthetic smoke tests catch gross failure; canary metrics detect real-workload regressions. Use bounded automatic halt/rollback rules plus human-readable diagnostics, and avoid gates based only on process-alive status.

10. **[Design] What is a safe database-migration sequence for independently deployed versions?**

    **Answer.** Expand first with additive nullable/default-compatible schema, deploy code that can read both forms and optionally dual-write/backfill, migrate/verify data in bounded resumable steps, switch reads, then remove old writes/columns only after all old binaries and rollback windows are gone. Coordinate locks and load, take/verify recovery points, and make migrations observable/idempotent. This pattern also applies to files and message schemas.

11. **[Deep dive] What is the difference between rollback and roll-forward?**

    **Answer.** Rollback deploys a known older artifact; roll-forward deploys a new fix that preserves compatibility with state already changed. Rollback is faster when binaries are stateless and compatibility holds. Roll-forward is necessary when data/protocol effects cannot be reversed or the old version has a severe vulnerability. Prepare both paths, define who decides, and rehearse recovery rather than inventing it during an incident.

12. **[Design] How should CI/CD access production?**

    **Answer.** Use short-lived workload identity or narrowly scoped credentials, protected environments with explicit policy/approval, isolated trusted runners, auditable actions, and least privilege. Separate build from deploy trust domains: compiling untrusted code must not grant production access. Pin/verify third-party pipeline actions, protect configuration changes through review, and support emergency access with time-bound audited break-glass procedures.

---

# Assessment usage notes

- Ask what state each Git command changes before asking for command syntax.
- For CI/CD scenarios, connect source, exact build inputs, artifact identity, deployment, health evidence, and recovery.
- C++-specific risks include ABI/toolchain mismatches, platform matrices, runtime libraries, symbol retention, sanitizer configurations, and statically embedded dependencies.
- Reward safe recovery and collaboration over clever history rewriting.
