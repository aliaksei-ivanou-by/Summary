# C++ Assessment — Complete Guide

This directory is a self-contained interview and study guide for modern C++ engineering. It expands the reusable question bank in `C++ Vacancy Preparation` into detailed model answers, code examples, diagrams, tradeoffs, and follow-up material.

## Guide map

| Guide | Questions | Main coverage |
| --- | ---: | --- |
| [C++ Core](C++%20Core.md) | 327 | Build/language model, types, lifetime, object model, RAII, templates, exceptions, undefined behavior, initialization, and compile-time tools |
| [STL](STL.md) | 216 | Containers, iterators, algorithms, vocabulary types, time, concurrency, C++20/C++23 library features, and lock-free design |
| [Computer Science](Computer%20Science.md) | 231 | CPU/memory, OS, processes, threads, networking, binaries, debugging, profiling, and performance engineering |
| [Software Design](Software%20Design.md) | 122 | Principles, GoF patterns, architecture, APIs, error models, ABI, distributed contracts, and resilience |
| [Algorithms and Data Structures](Algorithms%20and%20Data%20Structures.md) | 75 | Complexity, invariants, graph algorithms, search/window techniques, DSU, cycle detection, heaps, strings, and interval patterns |
| [Engineering Practices](Engineering%20Practices.md) | 69 | Make/CMake, dependencies, reproducible builds, testing, Linux production work, and native plug-ins |
| **Total** | **1,040** | Every numbered question has a model answer |

## Recommended study order

1. Start with **C++ Core** for the language and lifetime model.
2. Continue with **STL** to connect those rules to containers, algorithms, and concurrency facilities.
3. Study **Algorithms and Data Structures** while implementing the central patterns from memory and testing their invariants.
4. Use **Computer Science** for the machine, operating-system, network, and performance model underneath C++ programs.
5. Use **Software Design** for component and system boundaries, API evolution, and architectural tradeoffs.
6. Finish with **Engineering Practices** to cover how production C++ is built, tested, shipped, and diagnosed.

## Building an interview from the guide

A balanced 60–90 minute interview should sample depth instead of attempting broad trivia:

- one ownership/lifetime or object-model discussion;
- one STL/container/iterator correctness question;
- one coding problem with an explicit invariant and complexity analysis;
- one concurrency, OS, or networking scenario relevant to the role;
- one design/API tradeoff;
- one build, test, profiling, or production-diagnostic follow-up.

Begin with a basic question and deepen it using code, failure cases, and changed constraints. The model answers are reference material, not a checklist that every candidate must recite verbatim.

## Cross-topic scenarios

The most useful senior-level questions connect several guides:

- A `vector` reallocation exposes a dangling `string_view`: lifetime rules, iterator invalidation, and sanitizers.
- A queue grows during an outage: producer-consumer design, bounded backpressure, latency percentiles, and operational metrics.
- A plug-in crashes only in release builds: UB, ABI boundaries, dynamic loading, exact symbols, and dump analysis.
- A public header edit rebuilds the repository: include dependencies, templates, PImpl, CMake target propagation, and compiler caches.
- A TCP service processes duplicate requests: byte-stream framing, timeouts/retries, idempotency keys, schema evolution, and observability.
- A lock-free queue is slower than a mutex: memory ordering, cache-line contention, progress guarantees, workload shape, and measurement.

Strong answers distinguish standard guarantees from common implementations, state preconditions and ownership, quantify complexity or resource bounds, and identify what evidence would validate a performance or production claim.
