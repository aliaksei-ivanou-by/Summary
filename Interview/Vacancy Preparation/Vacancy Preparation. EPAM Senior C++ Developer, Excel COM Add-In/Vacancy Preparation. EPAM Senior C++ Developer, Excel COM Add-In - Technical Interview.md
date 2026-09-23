# EPAM Senior C++ Developer - Vacancy-Specific Technical Questions

> Vacancy-specific question bank for the EPAM Senior C++ Developer role. General knowledge lives in the shared banks: [C++](<../Technical Interview/C++ Core Questions.md>), [JavaScript and Node.js](<../Technical Interview/JavaScript and Node.js Questions.md>) and [COM and Excel](<../Technical Interview/COM and Excel Questions.md>). This file keeps only cross-stack and architecture questions specific to the vacancy.

> Main vacancy preparation: [EPAM Senior C++ Developer](<./Vacancy Preparation. EPAM Senior C++ Developer, Excel COM Add-In.md>).

# Shared Question Banks

| Bank | Coverage |
|---|---|
| [C++ Core Questions](<../Technical Interview/C++ Core Questions.md>) | Language, build/link model, STL, ownership, concurrency, algorithms, systems, performance and design |
| [C Language Questions](<../Technical Interview/C Language Questions.md>) | C89 and later standards, undefined behaviour, memory sections, alignment and packing, strings, function pointers, opaque structs, static and shared libraries, core dumps and sanitizers |
| [JavaScript and Node.js Questions](<../Technical Interview/JavaScript and Node.js Questions.md>) | JavaScript semantics, promises, event loop, Node.js, libuv, workers and streams |
| [COM and Excel Questions](<../Technical Interview/COM and Excel Questions.md>) | COM fundamentals, apartments/marshaling, add-in lifecycle, Excel object-model performance, add-in technologies, RTD and Office.js |
| [Testing Questions](<../Technical Interview/Testing Questions.md>) | Testing theory, GoogleTest fixtures and parameterized tests, GoogleMock, testing threads and time, legacy code, CI layering |
| [Linux and Shell Questions](<../Technical Interview/Linux and Shell Questions.md>) | Permissions, processes and signals, hung-process diagnosis, systemd, redirection, shell scripting |
| [Build Systems Questions](<../Technical Interview/Build Systems Questions.md>) | Make, target-based CMake, dependencies, cross-compilation, build speed |
| [Containers and Orchestration Questions](<../Technical Interview/Containers and Orchestration Questions.md>) | Docker and Kubernetes - prepared knowledge, low priority for this vacancy |
| [Live Coding Scaffold](<../Technical Interview/Live Coding Scaffold.md>) | CMake/GoogleTest scaffold, clarifying questions, and two exercises: a range block into native data, and coalescing a fast feed for a slow consumer |

# Question Index

Questions use stable topic-specific IDs. Every answer begins with a short bullet summary and keeps details, examples and edge cases below it.

## C++ and JavaScript Integration (ROLE-001–ROLE-004)

- [ROLE-001. How would you connect a C++ backend with JavaScript?](#question-role-001)
- [ROLE-002. JSON vs binary serialization?](#question-role-002)
- [ROLE-003. How should errors cross the C++/JS boundary?](#question-role-003)
- [ROLE-004. How do you avoid blocking the JS event loop with C++ work?](#question-role-004)

## Senior Architecture Scenarios (ROLE-005–ROLE-010)

- [ROLE-005. Excel freezes with 50,000 formulas. What do you do?](#question-role-005)
- [ROLE-006. You receive 20,000 market updates per second but Excel only needs 10 refreshes per second. How would you design it?](#question-role-006)
- [ROLE-007. A mutex fixed crashes but made the application much slower. What next?](#question-role-007)
- [ROLE-008. JavaScript requests a large calculation from C++. How would you design it?](#question-role-008)
- [ROLE-009. Excel COM call blocks while a background thread waits for Excel. What do you suspect?](#question-role-009)
- [ROLE-010. What architecture would you use for C++ + Excel + Node.js?](#question-role-010)

---

# 1. C++ and JavaScript Integration

## Question ROLE-001

[↑ Back to question index](#question-index)

### Question ROLE-001 — How would you connect a C++ backend with JavaScript?

**Short answer**

- Choose a native Node add-on/N-API for lowest in-process latency, IPC to a child/service for isolation, or a network API for deployment independence.
- Define a versioned data/error/lifetime contract, minimize copies and boundary crossings, and keep all heavy C++ calls asynchronous from JavaScript.
- In-process integration needs ABI/build/crash discipline; out-of-process integration adds serialization, transport failure, backpressure and recovery design.

**Details and nuances**

Possible approaches:

- Node native addon
- IPC
- named pipes
- sockets
- WebSocket
- shared memory
- subprocess communication

Choice depends on:

- latency
- throughput
- isolation
- deployment
- complexity
- failure boundaries

[↑ Back to question index](#question-index)

---

## Question ROLE-002

[↑ Back to question index](#question-index)

### Question ROLE-002 — JSON vs binary serialization?

**Short answer**

- JSON is readable and ubiquitous but verbose, text-parsing heavy and limited in native numeric/binary/type representation.
- Binary formats are usually smaller/faster and may provide schemas, but add tooling, versioning, endianness/alignment and compatibility concerns.
- Choose from measured payload/rate/latency and interoperability needs; define limits, validation and schema evolution for either format.

**Details and nuances**

JSON:

- simple
- human-readable
- broadly interoperable
- larger and slower

Binary formats:

- smaller
- faster
- often schema-based
- more complex

For very high-frequency data, binary formats or compact messages may significantly reduce overhead.

[↑ Back to question index](#question-index)

---

## Question ROLE-003

[↑ Back to question index](#question-index)

### Question ROLE-003 — How should errors cross the C++/JS boundary?

**Short answer**

- Never let a C++ exception cross a C ABI, Node callback or COM boundary; catch it at the owning layer and translate it.
- Define stable error codes/categories plus safe message/context, then expose synchronous JS throws or asynchronous Promise rejections consistently.
- Preserve cancellation vs timeout vs validation vs internal failure, clean up partially created native resources and avoid leaking sensitive internals.

**Details and nuances**

Define an explicit error contract.

Examples:

```text
status code
error category
message
context
```

Do not let raw C++ exceptions leak across ABI boundaries.

Translate exceptions into stable error values and reconstruct JS errors at the JS layer.

[↑ Back to question index](#question-index)

---

## Question ROLE-004

[↑ Back to question index](#question-index)

### Question ROLE-004 — How do you avoid blocking the JS event loop with C++ work?

**Short answer**

- Make the JS-facing function enqueue a native job and return a Promise/callback handle immediately; run CPU/blocking work on a bounded worker pool.
- Workers must not call JavaScript APIs directly; marshal completion back to the owning JS thread using the runtime's supported async mechanism.
- Define ownership roots, cancellation, shutdown, progress, queue limits and exception translation so neither JS objects nor native work outlive each other incorrectly.

**Details and nuances**

Do heavy work on:

- native worker threads
- thread pool
- worker process

Then post the result back asynchronously.

The JS-facing call should ideally start work and return quickly.

[↑ Back to question index](#question-index)

---

# 2. Senior Architecture Scenarios

## Question ROLE-005

[↑ Back to question index](#question-index)

### Question ROLE-005 — Excel freezes with 50,000 formulas. What do you do?

**Short answer**

- Measure whether time is calculation, COM traffic, volatile/dependency-heavy formulas, UI redraw/events or add-in callbacks; capture timing before redesigning.
- Replace per-cell calls with bulk `Value2`, reduce volatile/repeated formulas, calculate only necessary ranges and move pure computation to C++ on copied data.
- Temporarily changing calculation, events and screen updating requires guaranteed restoration of the *previous* values through RAII or `finally`, because they are global application state and an exception in between leaves the user's Excel frozen and event-deaf. See [COM-033](<../Technical Interview/COM and Excel Questions.md#question-com-033>).

**Details and nuances**

Start by measuring where time goes.

Potential causes:

- too many COM calls
- Excel recalculation
- UI-thread work
- lock contention
- excessive allocations
- inefficient data structures
- repeated conversion/serialization

Likely optimization directions:

```text
profile
↓
batch COM operations
↓
cache repeated values
↓
reduce recalculation
↓
move pure computation off UI thread
↓
aggregate updates
↓
measure again
```

[↑ Back to question index](#question-index)

---

## Question ROLE-006

[↑ Back to question index](#question-index)

### Question ROLE-006 — You receive 20,000 market updates per second but Excel only needs 10 refreshes per second. How would you design it?

**Short answer**

- Name the mechanism first: in Excel this is what an **RTD server** exists for. The feed thread coalesces into a latest-value-per-topic store and calls `UpdateNotify`; Excel then pulls through `RefreshData` at `Application.RTD.ThrottleInterval`, so the display rate is decoupled from the feed rate by the platform rather than by my own timer.
- If the product does not use RTD, the same shape by hand: a bounded latest-value-per-key store instead of a queue of obsolete ticks, a ~10 Hz scheduler that snapshots changed keys, builds range-shaped batches and posts one task to the Excel/STA thread. On Office.js the equivalent is a streaming custom function emitting at a chosen rate.
- Either way, define backpressure and drop policy, ordering and stale-data indication; instrument ingress rate, coalescing ratio, queue age, refresh latency and shutdown.

**Details and nuances**

Use a pipeline:

```text
market feed
    ↓
background receiver
    ↓
latest-value cache / aggregation
    ↓
coalescing
    ↓
bounded queue
    ↓
timer every ~100 ms
    ↓
batch update Excel
```

Key ideas:

- do not render every intermediate value
- separate ingestion rate from UI refresh rate
- use backpressure
- keep COM calls on the correct apartment/thread

The reason to name RTD out loud is that it changes what the question is about. Described generically, this is a producer/consumer rate-mismatch answer that could apply to any UI. Named, it says I know which Excel mechanism owns the problem, and the conversation moves to its limits - `RefreshData` runs on Excel's thread and must return quickly, topic count is the scaling dimension, and the throttle interval is a user-visible application setting rather than something the add-in owns. See [COM-035](<../Technical Interview/COM and Excel Questions.md#question-com-035>) and [COM-036](<../Technical Interview/COM and Excel Questions.md#question-com-036>).

If the interviewer says the product is Office.js rather than native, the answer maps across without changing shape: a streaming custom function with `setResult`, coalescing on my side, and `onCanceled` handled so a removed cell does not leak a subscription. See [COM-040](<../Technical Interview/COM and Excel Questions.md#question-com-040>).

[↑ Back to question index](#question-index)

---

## Question ROLE-007

[↑ Back to question index](#question-index)

### Question ROLE-007 — A mutex fixed crashes but made the application much slower. What next?

**Short answer**

- Keep the correctness fix, then measure contention, hold time, wait time and the exact protected invariant before changing synchronization.
- Reduce work under lock, avoid COM/I/O/callbacks inside it, batch operations, shard independent state or move ownership to a single consumer.
- Use atomics/read-write/lock-free techniques only when the state model fits and benchmarks prove improvement; rerun race and correctness tests after every change.

**Details and nuances**

Profile contention first.

Possible improvements:

- reduce critical-section scope
- avoid global lock
- partition state
- reader/writer strategy if suitable
- queue ownership to a single consumer
- atomics for independent state
- batch operations under one lock
- avoid holding locks during COM or I/O calls

Do not replace the mutex with lock-free code automatically.

[↑ Back to question index](#question-index)

---

## Question ROLE-008

[↑ Back to question index](#question-index)

### Question ROLE-008 — JavaScript requests a large calculation from C++. How would you design it?

**Short answer**

- Return a Promise/job handle immediately and run native computation on a bounded worker pool using immutable or explicitly owned input data.
- Post progress/result/error back to the JS thread; never invoke JS APIs from arbitrary native workers.
- Specify cancellation, timeouts, queue limits, copy/transfer strategy and shutdown lifetime, and make late completion after JS teardown harmless.

**Details and nuances**

Prefer asynchronous execution:

```text
JS call
  ↓
enqueue native job
  ↓
C++ worker thread(s)
  ↓
result
  ↓
post completion
  ↓
Promise resolve/reject
```

Requirements:

- do not block JS event loop
- define cancellation
- define lifetime ownership
- convert errors explicitly
- avoid unnecessary copies
- consider batching

[↑ Back to question index](#question-index)

---

## Question ROLE-009

[↑ Back to question index](#question-index)

### Question ROLE-009 — Excel COM call blocks while a background thread waits for Excel. What do you suspect?

**Short answer**

- Suspect a circular dependency involving the Excel STA/UI thread, a worker wait, missing message pumping or marshaled COM call.
- Capture all stacks and map: which thread owns Excel, which locks each holds, who waits for whom, and whether a callback/retry is pending.
- Remove synchronous UI↔worker waiting, never hold shared locks across COM, marshal work one-way to the STA and return results asynchronously.

**Details and nuances**

Potential COM/UI deadlock.

Check:

- apartment ownership
- whether UI thread is pumping messages
- whether worker holds a mutex needed by UI
- whether UI waits for worker while worker waits for COM/UI
- cross-apartment marshaling
- lock order

A classic bad pattern is:

```text
UI thread waits for worker
worker calls Excel COM object
COM needs UI/STA thread
=> deadlock
```

[↑ Back to question index](#question-index)

---

## Question ROLE-010

[↑ Back to question index](#question-index)

### Question ROLE-010 — What architecture would you use for C++ + Excel + Node.js?

**Short answer**

- Separate adapters from a testable C++ core: Excel/COM stays on its STA thread, Node stays responsive, and workers own CPU-heavy/stateful processing.
- Connect processes/layers with a versioned asynchronous protocol, explicit ownership/errors/cancellation, bounded queues, batching and coalescing.
- Choose in-process vs service boundaries from latency and fault-isolation needs; add health, metrics, restart and orderly shutdown from the beginning.

**Details and nuances**

One reasonable model:

```text
Remote API / Market feed
        ↓
Node.js / network layer
        ↓
async IPC
        ↓
C++ processing engine
        ↓
aggregation / cache
        ↓
Excel COM Add-In
        ↓
Excel UI
```

Important boundaries:

- Node event loop must stay responsive
- C++ handles CPU-heavy processing
- IPC contract should be explicit
- Excel COM access should respect apartment/thread requirements
- data should be batched and coalesced
- errors and shutdown must propagate cleanly

[↑ Back to question index](#question-index)
