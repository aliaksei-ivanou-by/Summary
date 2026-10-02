# C++ Assessment — Complete Guide

This directory is a self-contained interview and study guide for modern C++ engineering. It expands the reusable question bank in `C++ Vacancy Preparation` into detailed model answers, code examples, diagrams, tradeoffs, and follow-up material.

## Guide map

| Guide | Questions | Main coverage |
| --- | ---: | --- |
| [C++ Core](C++%20Core.md) | 375 | Build/language model, types, lifetime, object model, inheritance, polymorphism, operators, RAII, templates, exceptions, undefined behavior, initialization, and compile-time tools |
| [STL](STL.md) | 218 | Containers, iterators, algorithms, vocabulary types, time, concurrency, C++20/C++23 library features, and lock-free design |
| [Computer Science](Computer%20Science.md) | 232 | CPU/memory, OS, processes, threads, networking, binaries, debugging, profiling, and performance engineering |
| [Software Design](Software%20Design.md) | 122 | Principles, GoF patterns, architecture, APIs, error models, ABI, distributed contracts, and resilience |
| [Algorithms and Data Structures](Algorithms%20and%20Data%20Structures.md) | 85 | Complexity, invariants, balanced and augmented search trees, graph algorithms, search/window techniques, DSU, cycle detection, heaps, strings, interval patterns, and integrated implementation exercises |
| [Engineering Practices](Engineering%20Practices.md) | 69 | Make/CMake, dependencies, reproducible builds, testing, Linux production work, and native plug-ins |
| [Git and CI/CD](Git%20and%20CI-CD.md) | 55 | Git internals and recovery, collaboration, C++ build matrices, artifacts, supply-chain controls, releases, and deployment |
| [Databases and SQL](Databases%20and%20SQL.md) | 60 | Relational modelling, SQL, indexes and plans, transactions, concurrency, migrations, and C++ database integration |
| [Containers and Orchestration](Containers%20and%20Orchestration.md) | 55 | Docker/kernel fundamentals, native image builds, runtime security and debugging, resources, networking, and Kubernetes |
| **Total** | **1,271** | Every numbered question has a model answer |

## Recommended study order

1. Start with **C++ Core** for the language and lifetime model.
2. Continue with **STL** to connect those rules to containers, algorithms, and concurrency facilities.
3. Study **Algorithms and Data Structures** while implementing the central patterns from memory and testing their invariants.
4. Use **Computer Science** for the machine, operating-system, network, and performance model underneath C++ programs.
5. Add **Databases and SQL** when the role stores state or integrates with data services.
6. Use **Software Design** for component and system boundaries, API evolution, and architectural tradeoffs.
7. Continue with **Engineering Practices** for builds, tests, Linux production work, and native plug-ins.
8. Study **Git and CI/CD** for collaborative history, repeatable validation, artifact provenance, releases, and recovery.
9. Finish with **Containers and Orchestration** when applications are packaged or operated as services.

## Building an interview from the guide

A balanced 60–90 minute interview should sample depth instead of attempting broad trivia:

- one ownership/lifetime or object-model discussion;
- one STL/container/iterator correctness question;
- one coding problem with an explicit invariant and complexity analysis;
- one concurrency, OS, or networking scenario relevant to the role;
- one design/API tradeoff;
- one build, test, Git, data, delivery, or production-diagnostic follow-up.

Begin with a basic question and deepen it using code, failure cases, and changed constraints. The model answers are reference material, not a checklist that every candidate must recite verbatim.

## Cross-topic scenarios

The most useful senior-level questions connect several guides:

- A `vector` reallocation exposes a dangling `string_view`: lifetime rules, iterator invalidation, and sanitizers.
- A matrix is correct but unexpectedly slow: contiguous versus jagged storage, row-major traversal, cache locality, row proxies, and move/noexcept behavior.
- A derived overload hides a base overload: scope lookup, overload resolution, `override`, `using Base::function`, conversions, and API evolution.
- A queue grows during an outage: producer-consumer design, bounded backpressure, latency percentiles, and operational metrics.
- A plug-in crashes only in release builds: UB, ABI boundaries, dynamic loading, exact symbols, and dump analysis.
- A public header edit rebuilds the repository: include dependencies, templates, PImpl, CMake target propagation, and compiler caches.
- A TCP service processes duplicate requests: byte-stream framing, timeouts/retries, idempotency keys, schema evolution, and observability.
- A lock-free queue is slower than a mutex: memory ordering, cache-line contention, progress guarantees, workload shape, and measurement.
- `std::sort` beats `qsort` in a microbenchmark: type erasure, indirect comparator calls, template inlining, code size, and benchmark design.
- A cache policy looks good on one trace: workload representativeness, capacity curves, hit cost, and comparison with an offline-optimal oracle.
- An order-statistics tree passes small tests but fails at scale: balance invariants, metadata repair during rotations, iterative destruction, copy semantics, and logarithmic complexity.
- A polygon exposes its vertex container as a mutable reference: class invariants, alias lifetime, validation, cache invalidation, and API evolution.
- An unnamed-namespace helper is placed in a header: per-translation-unit identity, duplicated state/code, ODR risk, and unity-build behavior.
- A database-backed command times out during commit: RAII transactions, unknown outcomes, idempotency, retries, and the outbox pattern.
- A container works locally but crashes in production: image digest, ABI/runtime libraries, CPU architecture, cgroup limits, signals, and symbols.
- A release must be rolled back after a schema change: immutable artifacts, expand/migrate/contract, mixed-version compatibility, and roll-forward decisions.

Strong answers distinguish standard guarantees from common implementations, state preconditions and ownership, quantify complexity or resource bounds, and identify what evidence would validate a performance or production claim.
