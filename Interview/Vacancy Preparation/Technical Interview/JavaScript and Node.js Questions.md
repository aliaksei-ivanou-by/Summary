# JavaScript and Node.js Technical Interview Questions and Answers

> Reusable JavaScript and Node.js question bank. Vacancy-specific files should link here and keep only integration or product-specific scenarios locally.

# Question Index

Questions use stable topic-specific IDs. Every answer begins with a short bullet summary and keeps details, examples and edge cases below it.

## JavaScript Fundamentals (JS-001–JS-011)

|  |  |  |
|---|---|---|
| [JS-001. `var` vs `let` vs `const`?](#question-js-001) | [JS-002. What is a closure?](#question-js-002) | [JS-003. What is hoisting?](#question-js-003) |
| [JS-004. What is the prototype chain?](#question-js-004) | [JS-005. How does `this` work in JavaScript?](#question-js-005) | [JS-006. `==` vs `===`?](#question-js-006) |
| [JS-007. What is a Promise?](#question-js-007) | [JS-008. How does `async/await` work?](#question-js-008) | [JS-009. What is the JavaScript event loop?](#question-js-009) |
| [JS-010. What does this print, and in what order: sync, microtask, timer?](#question-js-010) | [JS-011. Is JavaScript really single-threaded?](#question-js-011) |  |

## Node.js (JS-012–JS-016)

|  |  |  |
|---|---|---|
| [JS-012. What is the Node.js event loop?](#question-js-012) | [JS-013. What is libuv?](#question-js-013) | [JS-014. Why can CPU-heavy code be a problem in Node.js?](#question-js-014) |
| [JS-015. What are Worker Threads?](#question-js-015) | [JS-016. What are Node.js streams?](#question-js-016) |  |

# 1. JavaScript Fundamentals

## Question JS-001

[↑ Back to question index](#question-index)

### Question JS-001 — `var` vs `let` vs `const`?

**Short answer**

- `var` is function-scoped, permits redeclaration and is initialized to `undefined` before its line; `let` and `const` are block-scoped and remain in the temporal dead zone until initialized.
- `const` prevents rebinding, not mutation of the referenced object; `let` is for intentional reassignment.
- Default to `const`, use `let` when state must change and avoid `var` in modern code unless its legacy semantics are specifically needed.

**Details and nuances**

`var`:

- function-scoped
- hoisted
- allows redeclaration

`let`:

- block-scoped
- temporal dead zone
- reassignable

`const`:

- block-scoped
- binding cannot be reassigned

`const` does not make the referenced object immutable.

[↑ Back to question index](#question-index)

---

## Question JS-002

[↑ Back to question index](#question-index)

### Question JS-002 — What is a closure?

**Short answer**

- A closure is a function together with access to the lexical environment in which it was created, even after the outer call returns.
- Captured variables are live bindings, not frozen value snapshots; loop declaration style and later mutation can therefore change results.
- Closures enable encapsulation and callbacks but can retain large object graphs, so clear long-lived listeners/timers and capture only needed state.

**Details and nuances**

A closure is a function together with access to variables from its lexical scope even after the outer function has returned.

Example:

```js
function counter() {
    let value = 0;

    return () => ++value;
}
```

The returned function keeps access to `value`.

[↑ Back to question index](#question-index)

---

## Question JS-003

[↑ Back to question index](#question-index)

### Question JS-003 — What is hoisting?

**Short answer**

- “Hoisting” describes how declarations are instantiated before normal statement execution; code is not literally moved.
- Function declarations are callable early, `var` exists as `undefined`, while `let`/`const`/`class` exist but cannot be accessed in their temporal dead zone.
- Function expressions follow their variable's rules, so a `const` arrow function is not callable before initialization.

**Details and nuances**

Declarations are processed before execution, but behavior differs.

Function declarations are available earlier.

`var` is hoisted and initialized to `undefined`.

`let` and `const` are hoisted but remain unavailable in the temporal dead zone until their declaration is executed.

[↑ Back to question index](#question-index)

---

## Question JS-004

[↑ Back to question index](#question-index)

### Question JS-004 — What is the prototype chain?

**Short answer**

- Property lookup checks an object's own properties and then follows its internal prototype links until a property is found or the chain ends at `null`.
- JavaScript `class` syntax builds on prototypes: instance methods live on the constructor's `.prototype`, while own fields live on each instance.
- Writes usually create/update an own property; getters/setters and proxies can alter that behavior, and mutating global prototypes is risky.

**Details and nuances**

JavaScript objects can inherit properties from another object via their internal prototype link.

Property lookup walks:

```text
object
↓
prototype
↓
prototype's prototype
↓
...
↓
null
```

ES6 `class` syntax is largely built on top of the prototype model.

[↑ Back to question index](#question-index)

---

## Question JS-005

[↑ Back to question index](#question-index)

### Question JS-005 — How does `this` work in JavaScript?

**Short answer**

- For ordinary functions, `this` is chosen by the call form: method receiver, `call`/`apply`, bound function, constructor call or default binding.
- Detaching a method loses its receiver; strict-mode default `this` is `undefined`, while legacy non-strict rules may use the global object.
- Arrow functions have no own `this` and capture the surrounding value, which is useful for callbacks but unsuitable when dynamic receivers are required.

**Details and nuances**

For normal functions, `this` depends primarily on how the function is called.

Arrow functions do not bind their own `this`; they capture lexical `this` from the surrounding scope.

[↑ Back to question index](#question-index)

---

## Question JS-006

[↑ Back to question index](#question-index)

### Question JS-006 — `==` vs `===`?

**Short answer**

- `===` compares type and value without coercing between types; `==` applies specified coercion rules that produce non-obvious cases.
- Prefer `===`/`!==` for application logic; use loose equality only when the coercion is deliberate and documented, such as `x == null` matching nullish values.
- `Object.is` differs for `NaN` and signed zero, while objects under all three comparisons are compared by identity.

**Details and nuances**

`===` first requires the operands to have the same type, then compares their values. `==` follows the abstract equality algorithm and may convert one or both operands before comparing them. That makes individually explainable rules compose into surprising results:

```js
0 == false          // true
'' == 0             // true
'0' == false        // true
null == undefined   // true
[] == 0             // true: [] -> '' -> 0
```

With `===`, all five comparisons are false. Objects are a separate point: equality compares identity, not structure, under either operator, so `[] === []` and `[] == []` are both false.

The usual rule is therefore `===` and `!==`. The useful deliberate exception is `value == null`, which concisely accepts exactly `null` or `undefined` in normal JavaScript; write it only if that intent is clear. Do not confuse equality with truthiness: `if (value)` also rejects `0`, `''`, `false` and `NaN`.

Even strict equality has two edge cases: `NaN === NaN` is false, while `+0 === -0` is true. `Object.is` reverses those two decisions: it recognizes `NaN` as itself and distinguishes signed zero.

[↑ Back to question index](#question-index)

---

## Question JS-007

[↑ Back to question index](#question-index)

### Question JS-007 — What is a Promise?

**Short answer**

- A Promise represents one eventual fulfilled value or rejection; once settled, its state cannot change.
- `.then`/`.catch`/`.finally` return new promises, and their handlers run asynchronously as microtasks; thrown errors become rejections.
- A Promise is not the operation itself and has no built-in cancellation—design cancellation with `AbortSignal` or an explicit job protocol.

**Details and nuances**

A Promise is a state machine with three states: **pending**, **fulfilled** with a value, or **rejected** with a reason. Fulfilled and rejected are collectively *settled*, and settlement is permanent. Resolving a promise with another promise or thenable makes it adopt that object's eventual state; resolution is therefore not always the same as immediate fulfillment.

The executor passed to `new Promise(...)` runs synchronously. In contrast, handlers registered with `.then`, `.catch` and `.finally` never run inline: a settled promise queues them as microtasks after the current JavaScript job finishes ([JS-010](#question-js-010)).

Every `.then` returns a **new** promise, which is why chains compose:

```js
fetchData()
    .then(parse)       // returned value fulfills the next promise
    .then(save)        // a returned promise is awaited/adopted
    .catch(report);    // a thrown exception becomes a rejection
```

Omitting a handler passes the value or rejection through. `.finally` is for cleanup and normally preserves the outcome; if it throws or returns a rejected promise, that new failure replaces the old outcome.

A Promise observes an operation; it does not itself create a thread, make synchronous work asynchronous, or provide cancellation. The underlying API must support cancellation explicitly, commonly through `AbortController`/`AbortSignal`. Rejections also need an intentional terminal handler or return path—starting a chain and discarding it can leave an unhandled rejection.

[↑ Back to question index](#question-index)

---

## Question JS-008

[↑ Back to question index](#question-index)

### Question JS-008 — How does `async/await` work?

**Short answer**

- An `async` function always returns a Promise; `await` suspends only that function and schedules its continuation as a microtask after settlement.
- Rejections are thrown at the await point and can be handled with `try/catch`; they do not block the event-loop thread.
- Independent operations should often start together and use `Promise.all`; awaiting each sequentially can add unnecessary latency and needs explicit partial-failure policy.

**Details and nuances**

An `async` function always returns a Promise.

`await` pauses execution of that async function until the awaited Promise settles, without blocking the entire JavaScript runtime thread.

Continuation is scheduled through the microtask mechanism.

[↑ Back to question index](#question-index)

---

## Question JS-009

[↑ Back to question index](#question-index)

### Question JS-009 — What is the JavaScript event loop?

**Short answer**

- JavaScript jobs run to completion on an execution thread; the runtime queues later work from timers, I/O and other event sources.
- After a task/callback, microtasks such as Promise reactions are drained before the next ordinary task, so an unbounded microtask chain can starve other work.
- Exact phases differ between browsers and Node.js, but CPU-heavy synchronous code blocks progress in either runtime.

**Details and nuances**

The event loop coordinates execution of:

- synchronous call stack
- task/macrotask queues
- microtask queue
- asynchronous I/O completions

JavaScript executes application code on one main thread, while runtime facilities can perform I/O or work elsewhere.

[↑ Back to question index](#question-index)

---

## Question JS-010

[↑ Back to question index](#question-index)

### Question JS-010 — What does this print, and in what order: sync, microtask, timer?

```js
console.log(1);

setTimeout(() => console.log(2), 0);

Promise.resolve().then(() => console.log(3));

console.log(4);
```

**Short answer**

- It prints `1`, `4`, `3`, `2` in that order.
- Synchronous statements finish first, the Promise reaction runs from the microtask queue, then the zero-delay timer runs in a later task/timer phase.
- A zero delay is a minimum scheduling request, not immediate execution; surrounding host-specific callbacks can affect broader examples.

**Details and nuances**

```text
1
4
3
2
```

The initial script is one job and runs to completion. It logs `1`, registers a timer, queues a Promise reaction, then logs `4`. Only after the call stack becomes empty can queued work run.

At that checkpoint the runtime drains the microtask queue, so the Promise reaction logs `3`. The timer callback belongs to a later task (or to the timers phase in Node.js), so it logs `2` afterwards. `setTimeout(..., 0)` means "eligible after at least this delay", not "run now"; the callback must still wait until the current job and its microtasks finish.

The robust rule for this example is:

```text
current synchronous job -> drain Promise microtasks -> next timer/task
```

Two qualifications prevent overgeneralizing it. First, a microtask may queue more microtasks, and the queue is drained again before moving on; an endless Promise chain can therefore starve timers and I/O. Second, host-specific queues matter in larger examples: Node.js gives `process.nextTick` its own higher-priority queue, and ordering between `setImmediate` and `setTimeout` depends on the context. Neither qualification changes `1, 4, 3, 2` for the code shown.

[↑ Back to question index](#question-index)

---

## Question JS-011

[↑ Back to question index](#question-index)

### Question JS-011 — Is JavaScript really single-threaded?

**Short answer**

- One JavaScript realm/event loop normally executes one callback at a time, which simplifies ordinary state access.
- The host still uses OS async I/O, helper pools and runtime threads; Web Workers/Node Worker Threads can run additional JavaScript concurrently.
- Shared memory through `SharedArrayBuffer` needs atomics, and a long synchronous callback still blocks its own event loop despite background capabilities.

**Details and nuances**

Main JavaScript execution typically runs on one thread.

But runtimes such as browsers and Node.js use:

- OS async facilities
- worker threads
- thread pools
- background runtime components

The important point is that the main JS event loop can still be blocked by CPU-heavy synchronous code.

[↑ Back to question index](#question-index)

---

# 2. Node.js

## Question JS-012

[↑ Back to question index](#question-index)

### Question JS-012 — What is the Node.js event loop?

**Short answer**

- Node's event loop lets one JavaScript thread coordinate many concurrent I/O operations by processing ready callbacks in phases.
- libuv/OS facilities perform or wait for much of the external work; Promise microtasks and `process.nextTick` have special scheduling between callbacks/phases.
- It is excellent for I/O concurrency but not CPU parallelism: long callbacks delay every other request and must be split or offloaded.

**Details and nuances**

Node.js executes JavaScript callbacks through an event loop.

Many I/O operations are asynchronous and are handled through OS mechanisms and libuv.

When results are ready, callbacks are queued for execution on the JavaScript thread.

[↑ Back to question index](#question-index)

---

## Question JS-013

[↑ Back to question index](#question-index)

### Question JS-013 — What is libuv?

**Short answer**

- libuv is Node.js's cross-platform event-loop and asynchronous I/O library, abstracting networking, timers, processes and filesystem/runtime facilities.
- It uses OS event mechanisms where possible and a worker pool for operations that lack suitable non-blocking APIs, including many filesystem/DNS/crypto tasks.
- The pool is finite, so slow queued work can delay unrelated users; it is not a general automatic parallelizer for JavaScript CPU code.

**Details and nuances**

libuv is the cross-platform library used by Node.js for:

- event loop
- async I/O abstraction
- timers
- filesystem operations
- networking
- thread pool tasks

[↑ Back to question index](#question-index)

---

## Question JS-014

[↑ Back to question index](#question-index)

### Question JS-014 — Why can CPU-heavy code be a problem in Node.js?

**Short answer**

- CPU-heavy synchronous JavaScript monopolizes the event-loop thread, increasing latency and preventing I/O callbacks, timers and new requests from progressing.
- Offload large work to a bounded Worker Thread/native/process pool, or chunk truly incremental work while preserving fairness.
- Account for serialization, queueing, cancellation and overload; spawning unlimited workers can replace event-loop blocking with CPU/memory collapse.

**Details and nuances**

CPU-heavy synchronous code blocks the event loop.

While it runs, Node cannot process other JavaScript callbacks efficiently.

Solutions can include:

- Worker Threads
- child processes
- native C++ worker threads
- chunking/batching work

[↑ Back to question index](#question-index)

---

## Question JS-015

[↑ Back to question index](#question-index)

### Question JS-015 — What are Worker Threads?

**Short answer**

- Worker Threads run JavaScript in additional isolates/threads and are intended mainly for CPU-bound parallel work, not ordinary asynchronous I/O.
- Data crosses by structured clone, transferable ownership or explicitly synchronized shared memory; normal objects are not implicitly shared.
- Use a reusable bounded pool because startup and message transfer cost matter, and define cancellation, errors and shutdown.

**Details and nuances**

A Node.js Worker Thread has its own V8 isolate, JavaScript heap and event loop on another OS thread. It can execute JavaScript in parallel with the main thread, but it is not a lightweight callback and does not implicitly share ordinary objects or module state. Unlike a child process, it remains in the same process and can deliberately share memory.

There are three important ways to move data across the boundary:

- `postMessage` normally uses the structured-clone algorithm, which copies supported values;
- a transferable such as an `ArrayBuffer` can move ownership without copying, detaching it from the sender;
- `SharedArrayBuffer` exposes the same bytes to both threads and requires `Atomics` or another sound synchronization protocol.

Workers are most useful for sufficiently large CPU-bound jobs: parsing, compression, image processing or computation that would otherwise block the event loop ([JS-014](#question-js-014)). They usually do not help ordinary network or filesystem I/O because Node already handles that asynchronously. Startup, cloning and message passing can cost more than a small job, so production code normally uses a bounded reusable pool and a bounded work queue rather than one worker per request.

The design still needs backpressure, error propagation, cooperative cancellation and deterministic shutdown. It also needs measurement: too many workers oversubscribe the CPU, while transferring huge results back to the main thread can simply move the bottleneck to serialization and result handling.

[↑ Back to question index](#question-index)

---

## Question JS-016

[↑ Back to question index](#question-index)

### Question JS-016 — What are Node.js streams?

**Short answer**

- Streams process data incrementally as readable, writable, duplex or transform flows, avoiding whole-payload buffering.
- Their key correctness/performance feature is backpressure: producers must honor `write()`/drain or use `pipe`/`pipeline` to match consumer speed.
- Handle errors, abort/cleanup and object-vs-byte mode explicitly; chunk boundaries are transport details, not necessarily application records.

**Details and nuances**

Streams process data incrementally instead of loading everything into memory at once.

Useful for:

- files
- network traffic
- large datasets
- real-time pipelines

Types include readable, writable, duplex and transform streams.

[↑ Back to question index](#question-index)
