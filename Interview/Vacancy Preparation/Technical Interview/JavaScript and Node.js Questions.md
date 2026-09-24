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

`const` does not make the referenced object immutable — it freezes the **binding**, not the value:

```js
const a = [1, 2];
a.push(3);        // fine - the array is mutable
a = [];           // TypeError - the binding is not
```

The C++ analogue is `T* const p`, not `const T* p`. For actual immutability use `Object.freeze` (shallow) or a structural-sharing library.

**`var`'s function scoping is the historical bug source**, and the loop is the canonical demonstration:

```js
for (var i = 0; i < 3; i++) setTimeout(() => console.log(i));  // 3 3 3 - one shared i
for (let i = 0; i < 3; i++) setTimeout(() => console.log(i));  // 0 1 2 - a fresh binding per iteration
```

`let` in a `for` head creates a **new binding each iteration**, which is a special rule worth naming—it is not merely block scoping.

**The temporal dead zone** is the region from the top of the block to the declaration. A `let` or `const` *is* hoisted—the binding exists—but touching it throws `ReferenceError` instead of quietly giving `undefined` ([JS-003](#question-js-003)). That turns a class of silent bugs into loud ones.

**`var` also creates a property on the global object** when declared at top level in a script (`var x = 1; window.x === 1`); `let` and `const` do not. And `var` allows redeclaration of the same name in the same scope, so a duplicated declaration in a long function silently overwrites.

**The rule in modern code:** `const` by default, `let` when the binding must change, `var` never. That is not style—`const` communicates that the binding is stable, which is real information for a reader, and the linter can enforce it (`prefer-const`, `no-var`).

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

The returned function keeps access to `value` — the variable outlives the call that created it, because the inner function holds a reference to the enclosing **environment record**, not a copy of the variable.

```js
const c1 = counter(), c2 = counter();
c1(); c1();   // 2
c2();         // 1  - separate environments, separate `value`
```

**The C++ comparison is the useful one here**: a closure is a lambda that captures by reference, but with **garbage-collected lifetime** — so there is no dangling-reference problem, which is exactly the hazard that makes `[&]` capture dangerous in C++ when the lambda outlives the frame. The trade-off is the opposite one: nothing is freed while a closure still refers to it.

**Which is the main practical hazard: closures are how you leak memory in JavaScript.** An event handler or interval callback that closes over a large object keeps that object alive as long as the handler is registered; removing the listener, or nulling the reference, is what releases it. V8 is smart enough to keep only the variables actually referenced — but a single reference to a big structure pins the whole thing.

**What it is used for**, beyond the counter example:

- **private state** — the module pattern, and still the way to get genuinely private data without `#fields`;
- **partial application** — `const add5 = x => x + 5` built from a factory;
- **callbacks that need context** — every `setTimeout`, event handler and promise continuation captures its surroundings, which is why async code in JavaScript reads sequentially at all;
- **memoisation** — the cache lives in the closure.

**The classic interview follow-up is the `var`-in-a-loop question** ([JS-001](#question-js-001)): three closures over *one* `var` binding print the same value; `let` gives each iteration its own binding. The pre-ES6 fix was an IIFE to create a fresh scope — which is the same mechanism, made explicit.

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

**The accurate mental model is two phases per scope.** On entry, the engine creates bindings for every declaration in that scope; then it executes statements. What differs is how each kind is *initialised* at creation time:

| Declaration | At scope entry | Before the declaration line |
|---|---|---|
| `function f(){}` | fully defined | callable |
| `var x` | initialised to `undefined` | `undefined` |
| `let` / `const` | created, **uninitialised** | `ReferenceError` (TDZ) |
| `class C {}` | created, uninitialised | `ReferenceError` |

```js
console.log(f());   // "ok"      - function declaration is fully hoisted
console.log(v);     // undefined - var is hoisted and initialised
console.log(l);     // ReferenceError - TDZ
function f(){ return "ok"; }
var v = 1;
let l = 2;
```

**Function *expressions* are not hoisted as functions** — `var f = function(){}` hoists `f` as `undefined`, so calling it early gives `f is not a function`, a different error from the TDZ one. The same for arrow functions assigned to `const`, which give a `ReferenceError` instead.

**`typeof` is not safe in the TDZ**, which is the detail that catches people: `typeof undeclaredVar` returns `"undefined"` harmlessly, but `typeof letVarInTDZ` throws.

**Function declarations inside blocks** are the messy corner: in strict mode (and modules) they are block-scoped; in sloppy mode the behaviour is a compatibility annex and differs between engines. Do not rely on it.

**Why the TDZ was designed in**: it turns "used before defined" from a silent `undefined` — which then flows through the program and fails somewhere else — into an immediate, located error ([JS-001](#question-js-001)). Writing declarations before use makes all of this moot, which is the actual advice.

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

ES6 `class` syntax is largely built on top of the prototype model — `class` is syntax over constructor functions and prototype objects, not a separate mechanism.

**Distinguish the two names**, because the question usually hinges on it: `obj.__proto__` (properly `Object.getPrototypeOf(obj)`) is the link the lookup follows; `Func.prototype` is the object that *instances of* `Func` will get as their prototype. They are different things that share a word.

```js
function Dog(name) { this.name = name; }
Dog.prototype.speak = function () { return this.name + " barks"; };

const d = new Dog("Rex");
Object.getPrototypeOf(d) === Dog.prototype;            // true
Object.getPrototypeOf(Dog.prototype) === Object.prototype;  // true
Object.getPrototypeOf(Object.prototype) === null;      // end of the chain
```

`new` does four things: create an object, set its prototype to `Dog.prototype`, run the constructor with `this` bound to it, and return it (unless the constructor returns an object).

**Reads walk the chain; writes do not.** `d.speak` is found on the prototype, but `d.speak = ...` creates an **own** property that shadows it — the prototype is untouched. That asymmetry explains most prototype confusion, including why mutating a shared prototype property from an instance appears to work for objects and not for primitives.

**The contrast with C++** is worth stating: this is delegation at *runtime* between live objects, not a compile-time class hierarchy. The prototype of an object can be replaced after creation (`Object.setPrototypeOf`) — which works, and which V8 punishes severely because it invalidates the hidden-class optimisation. Avoid it in hot code.

**Why it matters in practice:** `hasOwnProperty` versus `in` (own property vs anywhere on the chain); `for...in` walking inherited enumerable properties while `Object.keys` does not; and monkey-patching built-ins by writing to `Array.prototype`, which is the reason `for...in` over an array is unsafe.

**`class` adds real things**, not just sugar: methods are non-enumerable, the body is strict mode, calling a class without `new` throws, `extends` sets up both prototype links correctly, and `#private` fields are genuinely inaccessible — something the prototype model alone cannot express.

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

For an ordinary function `this` is decided at the **call site**, not where the function was written. The rules apply in a fixed precedence order, and being able to recite it is the point of the question:

1. `new f()` — `this` is the freshly created object.
2. `f.call(o)` / `f.apply(o)` / `f.bind(o)` — `this` is `o` (an explicit `bind` wins over a later `call`).
3. `o.f()` — `this` is `o`, the object *left of the dot*.
4. Otherwise — `undefined` in strict mode and in ES modules and class bodies; the global object (`globalThis`) in sloppy mode.

**The classic bug is detaching a method**, because rule 3 depends on the call expression rather than on the function:

```js
const counter = { n: 0, inc() { this.n++; } };
const f = counter.inc;
f();                       // TypeError in strict mode: this is undefined
setTimeout(counter.inc, 0) // same problem - the reference is passed, the receiver is not

setTimeout(() => counter.inc(), 0);      // fix 1: keep the call expression intact
setTimeout(counter.inc.bind(counter), 0) // fix 2: bind the receiver
```

Class methods are strict by definition, so a detached class method gives `undefined` rather than silently writing to the global object—which is why React class components needed `bind` in the constructor or class fields.

**Arrow functions have no `this` binding at all.** They are not "bound to the enclosing object"; they simply do not create the slot, so `this` resolves lexically through the scope chain exactly like any other variable. `call`/`apply`/`bind` cannot change it, and `new` on an arrow is a `TypeError`. That makes arrows right for callbacks inside a method and wrong for object literal methods (`{ f: () => this.x }` captures the *module* `this`, not the object) and for anything that needs a dynamic receiver, such as a DOM event handler that wants `this === event.currentTarget`.

**Coming from C++** the useful framing is that `this` is not a hidden first parameter fixed at definition like a member function's; it is closer to an implicit argument re-bound per call—so passing a method around loses it, the way passing a member function pointer without its object would.

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

`===` compares type first: different types, `false`, no conversion. `==` runs the abstract equality algorithm, which converts until the types match:

- `null == undefined` is `true`, and neither equals anything else—`null == 0` is `false`.
- string vs number: the string is converted to a number, so `"1" == 1` and `"" == 0` are `true`.
- boolean vs anything: the boolean becomes `0` or `1` *first*, so `"1" == true` is true but `"yes" == true` is false.
- object vs primitive: the object is converted with `valueOf`/`toString`, which is where the party tricks come from—`[] == 0` and `[] == ""` are true, and `[] == ![]` is true because `![]` is `false`, then `0`, and `[]` also converts to `0`.
- `NaN` equals nothing, including itself, under both operators.

**The one place loose equality is idiomatic** is `x == null`, which is true for exactly `null` and `undefined`—a deliberate, readable nullish check. Everywhere else use `===`; most style guides and ESLint's `eqeqeq` enforce this with a `null` exemption.

**Three comparisons, not two.** `Object.is` is `===` except that it treats `NaN` as equal to `NaN` and distinguishes `+0` from `-0`:

```js
NaN === NaN            // false
Object.is(NaN, NaN)    // true      (or use Number.isNaN)
0 === -0               // true
Object.is(0, -0)       // false
```

**Objects are compared by reference under all three.** `{a:1} === {a:1}` is `false`; there is no value equality operator, so deep comparison needs a library or a hand-written walk. This is the part a C++ engineer should flag explicitly, because there is no way to overload `==` for a class—unlike `operator==`, equality is not user-definable in JavaScript.

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

A Promise is a container for a result that does not exist yet. It is in one of three states—**pending**, **fulfilled** with a value, or **rejected** with a reason—and the transition out of pending happens exactly once and is irreversible. Registering a handler after it has settled is fine; the handler just runs on the next microtask checkpoint.

**The executor runs synchronously; only the handlers are deferred.** This surprises people:

```js
console.log('a');
new Promise(res => { console.log('b'); res(); }).then(() => console.log('d'));
console.log('c');
// a b c d
```

**`.then` returns a *new* promise**, which is what makes chaining work and what makes the return value of each handler meaningful: returning a plain value fulfils the next promise with it, returning a promise (or any thenable) adopts its eventual state—so chains flatten rather than nest—and throwing rejects it. That is why the C++ instinct to think of it as a `std::future` is only half right: `std::future` has no continuations and `.get()` blocks, whereas a promise never blocks and composes.

**Rejection propagates down the chain** until a handler with an `onRejected` argument catches it. Two details follow: `.then(f).catch(g)` catches errors thrown by `f`, while `.then(f, g)` does not; and a rejected promise nobody ever handles raises `unhandledrejection` (in Node, since v15 that terminates the process by default).

**`.finally`** runs on both paths and passes the settlement through unchanged—it is the cleanup hook, not a place to produce a result.

**Combinators** are where promises pay off: `Promise.all` fails fast on the first rejection, `allSettled` always resolves with per-item status, `race` settles with the first to settle either way, `any` takes the first *fulfilment* and rejects with an `AggregateError` only if all fail.

**A promise is not a handle to the work.** It cannot be cancelled and `all`'s fail-fast does not stop the other operations—they run to completion and their results are discarded. Cancellation has to be built into the operation itself, normally via `AbortController`/`AbortSignal`.

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

**It is a transformation, not a new concurrency model.** `async`/`await` is syntax over promises and generators: the function body is split at each `await` into a continuation that is scheduled as a microtask when the awaited promise settles ([JS-007](#question-js-007)).

```js
async function f() {
  console.log(1);          // runs SYNCHRONOUSLY when f() is called
  await something();       // everything after this is a microtask continuation
  console.log(2);
}
f(); console.log(3);       // 1, 3, 2
```

The body up to the first `await` runs immediately — a detail that matters when the function has side effects before the first suspension point.

**`await` on a non-promise still yields.** `await 5` wraps the value and defers the continuation by a microtask tick, so it is not a no-op ([JS-010](#question-js-010)).

**Error handling becomes ordinary `try/catch`**, which is the main readability win over `.then/.catch`. But two rejection traps follow:

```js
// sequential: 2 seconds, and a rejection of a() leaves b() unstarted
const x = await a(); const y = await b();

// concurrent: 1 second - start both, then await
const [x, y] = await Promise.all([a(), b()]);
```

Awaiting in a loop when the operations are independent is the most common performance bug in async JavaScript. And a promise created but awaited *later* can raise an unhandled-rejection warning in the gap.

**`await` inside a `forEach` does nothing** — `forEach` ignores the returned promise. Use `for...of` for sequential, or `Promise.all(map(...))` for concurrent.

**Top-level `await`** works in ES modules only, and it delays the module's evaluation for everything importing it.

**The framing for a C++ engineer**: this is cooperative, single-threaded concurrency — a coroutine that suspends and resumes on one thread, closer to C++20 coroutines than to `std::async`. Nothing runs in parallel, and nothing blocks; `await` releases the thread back to the event loop ([JS-009](#question-js-009)).

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

**The loop, stated as an algorithm:**

```text
1. take one task (macrotask) from the task queue and run it to completion
2. drain the ENTIRE microtask queue - including microtasks queued by microtasks
3. (browser) render if it is time to paint
4. repeat
```

**"Run to completion" is the defining property.** A task is never pre-empted, so no other JavaScript can observe a half-finished operation — which is why JavaScript needs no locks and has no data races on ordinary objects. The price is that a long task blocks *everything*: input handling, rendering, timers ([JS-014](#question-js-014)).

**The two queues are not the same queue**, and the distinction is the most common interview question in this area:

| Macrotasks (one per turn) | Microtasks (drained fully) |
|---|---|
| `setTimeout`, `setInterval` | promise reactions (`.then`, `await` continuations) |
| I/O callbacks, `setImmediate` (Node) | `queueMicrotask` |
| DOM events, `MessageChannel` | `MutationObserver` (browser), `process.nextTick` (Node, even earlier) |

So a promise chain always completes before the next timer, and **an unbounded microtask chain starves the loop completely** — the page hangs, timers never fire. That is a genuine hang, not slowness.

**Rendering is a budget, not an event.** The browser paints at most once per frame (~16 ms at 60 Hz) and only between tasks, so a 50 ms task drops frames regardless of how fast the rest of the code is. Splitting long work across tasks (`setTimeout(…, 0)`, `scheduler.yield`) is what keeps a UI responsive.

**Node differs in structure** — it has phases rather than one task queue, plus `process.nextTick` ahead of promises ([JS-012](#question-js-012)) — but the run-to-completion and microtask-drain rules are identical.

**For a C++ engineer**: it is the message-pump model, the same shape as a Win32 `GetMessage`/`DispatchMessage` loop or an STA's COM dispatch — and it fails the same way when one handler blocks.

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

The mechanism, in the order the engine applies it:

1. The whole script is one task. `console.log(1)` and `console.log(4)` are plain synchronous statements, so they run to completion first. `setTimeout` and `.then` only *schedule*—they return immediately.
2. When the call stack empties, the engine drains the **microtask queue** completely. The promise reaction prints `3`. If that handler had queued further microtasks, they would also run now, before anything else—an infinite microtask chain starves the loop entirely, which is a real hang, not a slowdown.
3. Only then does the loop proceed to the next macrotask: the timer callback prints `2`.

**`setTimeout(f, 0)` is a minimum delay, not "now".** Browsers clamp nested timeouts to ~4 ms after five levels, and Node's timer resolution means a 0 is treated as 1 ms. So the ordering guarantee here is "microtasks before timers", not "timers are slow".

**The same reasoning with `async/await`**, which is the usual follow-up:

```js
async function f() { console.log('a'); await null; console.log('b'); }
f(); console.log('c');
// a c b
```

An `async` function body runs synchronously up to the first `await`; everything after it is a microtask continuation, even when awaiting a non-promise.

**In Node there is one extra layer.** `queueMicrotask`/promise reactions and `process.nextTick` are two separate queues, and the `nextTick` queue is drained *first*. `setImmediate` versus `setTimeout(...,0)` at top level is genuinely non-deterministic because it depends on how long the loop took to start, but inside an I/O callback `setImmediate` always wins—it runs in the check phase of the same iteration ([JS-012](#question-js-012)).

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

**Precise answer: the *language* is single-threaded; the *runtime* is not.** One JavaScript execution context runs one piece of code at a time, so ordinary objects need no locks and cannot be seen half-updated. Everything else around it is threaded:

- **libuv's thread pool** — four threads by default — runs filesystem work, `dns.lookup`, and async crypto/zlib ([JS-013](#question-js-013));
- **the kernel** does socket I/O via epoll/kqueue/IOCP, with no thread at all;
- **V8's own threads** handle optimising compilation and garbage collection concurrently;
- **Worker Threads** and browser Web Workers run real JavaScript in parallel — but in **separate isolates** with no shared objects ([JS-015](#question-js-015)).

**So the concurrency model is: parallel I/O, concurrent-but-not-parallel JavaScript.** Ten thousand open sockets cost no threads; one 200 ms computation stalls all ten thousand.

**The one place real shared-memory concurrency exists** is `SharedArrayBuffer` plus `Atomics`, which gives genuine data races and needs the same reasoning as `std::atomic` — and even then only raw bytes are shared, never objects.

**Why this design was chosen**: it removes an entire class of bugs. For a C++ engineer the trade is explicit — you give up parallelism within one context and get, in exchange, no mutexes, no data races on application state, and no need to reason about memory ordering. What you must reason about instead is **not blocking**, which is a different and usually easier discipline ([JS-014](#question-js-014)).

**And the caveat that makes it not entirely free:** the guarantee is per *turn*, not per function. Code before and after an `await` is in two different turns, so other code runs in between and can change shared state — a logical race condition, without a data race. That is exactly the same distinction C++ draws ([JS-008](#question-js-008)).

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

**Node's loop has phases**, unlike the browser's single task queue, and each iteration visits them in order:

```text
timers          -> setTimeout / setInterval callbacks whose time has come
pending         -> some deferred system callbacks (e.g. TCP errors)
idle, prepare   -> internal
poll            -> wait for I/O; run I/O callbacks (this is where the loop spends its time)
check           -> setImmediate callbacks
close           -> 'close' events (socket.on('close'), etc.)
```

**Between every phase — and between individual callbacks — two queues are drained**, in this order: `process.nextTick` first, then promise microtasks. `nextTick` running ahead of promises is Node-specific and is why a recursive `process.nextTick` can starve the loop entirely, while a recursive `setImmediate` cannot.

**The classic question is `setImmediate` versus `setTimeout(fn, 0)`:**

```js
// at top level: order is NON-deterministic - it depends on how long startup took
setTimeout(() => console.log('timeout'), 0);
setImmediate(() => console.log('immediate'));

// inside an I/O callback: 'immediate' ALWAYS wins
fs.readFile(f, () => { setTimeout(()=>console.log('timeout'),0); setImmediate(()=>console.log('immediate')); });
```

The second case is deterministic because an I/O callback runs in the poll phase, and `check` comes immediately after it in the same iteration, whereas `timers` has already passed.

**`setTimeout(fn, 0)` is really 1 ms** in Node, and timers fire *no earlier* than their delay — the loop may be busy elsewhere, so the guarantee is a lower bound only.

**Watch the loop, don't guess at it.** `perf_hooks.monitorEventLoopDelay()` gives a histogram of loop lag, which is the single most useful production metric for a Node service: rising lag means something is blocking ([JS-014](#question-js-014)). `--prof`/`--cpu-prof` finds what.

**The browser loop is the same idea with different machinery** — one task queue plus a rendering step, and no `nextTick` ([JS-009](#question-js-009)).

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

libuv is the C library underneath Node: it owns the event loop, normalises the platform's readiness/completion APIs—epoll on Linux, kqueue on macOS/BSD, IOCP on Windows—and provides timers, child processes, signals, TTY and the thread pool.

**The distinction that actually gets asked: which work is truly async, and which is faked with threads.**

*Kernel-backed, no thread used*: TCP/UDP sockets, pipes, TTY. The loop just waits for readiness and does the syscall itself, so tens of thousands of connections cost no threads.

*Thread pool*: filesystem operations (there is no portable async file I/O), `dns.lookup` (it calls `getaddrinfo`, which is blocking—`dns.resolve` uses the network and does not), and the async crypto and zlib calls (`pbkdf2`, `randomBytes`, `scrypt`, `gzip`).

**The pool is small and shared—four threads by default.** That is the practical consequence worth stating: four concurrent `pbkdf2` calls will delay an unrelated `fs.readFile`, because they queue in the same place. `UV_THREADPOOL_SIZE` raises it (up to 1024) and must be set before the pool is first used, i.e. at process start, not after the first async call.

**The loop phases** are timers → pending callbacks → poll → check (`setImmediate`) → close callbacks, with the microtask and `nextTick` queues drained between each ([JS-012](#question-js-012)). Completion of a pool task is signalled back into the poll phase, so a pool thread never runs your JavaScript—the callback still executes on the main thread, which is why libuv gives you parallel *I/O*, not parallel *JavaScript*. For that you need Worker Threads ([JS-015](#question-js-015)).

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

**The mechanism, stated plainly:** a task runs to completion and is never pre-empted ([JS-009](#question-js-009)). While a 500 ms `JSON.parse` of a large payload runs, *nothing else happens* — no timers, no I/O callbacks, no new connections accepted, no health-check response. In a server handling 1 000 requests per second, that one request adds up to 500 ms of latency to **every** other in-flight request. That is the part that makes it a systems problem rather than a slow function.

**The usual culprits are not obviously "computation":**

- `JSON.parse`/`stringify` on megabyte payloads;
- synchronous `fs` calls — `readFileSync`, and `existsSync` in a loop;
- synchronous crypto: `crypto.pbkdf2Sync`, `randomBytes` without a callback;
- a catastrophically backtracking regex on attacker-supplied input (ReDoS) — a genuine denial-of-service vector;
- big `Array.sort`, `map`/`filter` chains over hundreds of thousands of elements;
- template rendering and image processing in pure JavaScript.

**Choosing the fix:**

| | When |
|---|---|
| **Worker Threads** | CPU work in JS; keep a bounded pool, and transfer `ArrayBuffer`s instead of cloning ([JS-015](#question-js-015)) |
| **Native addon (N-API)** | the work is already C++ — run it on a libuv pool thread and call back; this is the natural route for a C++ engineer |
| **Child process / separate service** | heavy, isolated, or crash-prone work; also gives you independent scaling |
| **Chunking** | the work is divisible and must stay in the main isolate — yield every N items with `setImmediate` so the loop breathes |
| **Async alternatives** | use the callback form: `crypto.pbkdf2`, `zlib.gzip`, streaming JSON ([JS-016](#question-js-016)) |

**Measure it rather than guessing**: `perf_hooks.monitorEventLoopDelay()` gives a lag histogram, and a P99 loop delay above a few milliseconds means something is blocking ([JS-012](#question-js-012)). `--cpu-prof` or `clinic doctor` identifies what.

**Note that the libuv thread pool does not solve this** — it handles I/O-style work, has only four threads by default, and saturating it delays unrelated filesystem and DNS operations ([JS-013](#question-js-013)).

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

A worker is a real OS thread running its **own V8 isolate and its own event loop**. Nothing is shared implicitly: no globals, no module cache, no prototypes. That isolation is what makes it safe, and also what makes it more expensive than a thread in C++—expect on the order of milliseconds and several megabytes per worker, so you create a pool at startup rather than a worker per task.

```js
const { Worker, isMainThread, parentPort, workerData } = require('node:worker_threads');
```

**Three ways data crosses, with very different costs.**

*Structured clone* is the default for `postMessage`: a deep copy that handles cycles, `Map`/`Set`/`Date`/typed arrays, but not functions, class identity or DOM-style handles. Cost is proportional to payload size, so a large object graph per message will eat the gain.

*Transfer* moves ownership instead of copying—`postMessage(buf, [buf])` on an `ArrayBuffer` or `MessagePort` is O(1) and leaves the sender's buffer detached (`byteLength === 0`). This is the right mechanism for bulk numeric data.

*`SharedArrayBuffer`* is genuinely shared memory with no copy at all, but then you own the synchronisation: `Atomics.load`/`store`/`add` and `Atomics.wait`/`notify`. The mental model maps directly onto `std::atomic` and a futex, and the same reasoning about data races applies—the only difference is that JavaScript has no way to share ordinary objects, so the shared region is always raw bytes plus a typed-array view.

**When *not* to use them.** Not for I/O—libuv already does that without blocking ([JS-013](#question-js-013)). Not for a single short task, because startup dominates. Use them when a synchronous CPU-bound step would otherwise block the loop and stall every other request ([JS-014](#question-js-014)).

**The comparison to have ready**: `worker_threads` shares the process (cheap messaging, shared memory possible); `child_process`/`fork` gives full isolation at higher IPC cost and is what you want for untrusted or crash-prone code; `cluster` forks whole processes sharing a listening socket, which is for scaling I/O-bound servers across cores, not for offloading computation.

**Operationally**, a pool needs the boring parts defined up front: a bounded queue, a per-task timeout, `worker.on('error')` and `on('exit')` to replace a dead worker, and `worker.terminate()` (which returns a promise) on shutdown—otherwise the process will not exit.

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

**The argument for them is memory and latency, together.** `fs.readFile` on a 2 GB file allocates 2 GB before the first byte is usable; a stream processes it in 64 KB chunks with constant memory and produces output immediately. On a server, ten concurrent whole-file reads is twenty gigabytes; ten streams is a few megabytes.

```js
const { pipeline } = require('node:stream/promises');
await pipeline(
  fs.createReadStream('in.csv'),
  zlib.createGzip(),
  fs.createWriteStream('out.csv.gz')
);
```

**Use `pipeline`, not `.pipe()`.** `.pipe()` does not forward errors and does not clean up the other streams when one fails — a failed write leaves the read stream open, which is how file-descriptor leaks happen. `pipeline` propagates errors and destroys every stream in the chain.

**Backpressure is the concept the question is really testing** (see the C++ Core bank): `writable.write()` returns `false` when its internal buffer exceeds `highWaterMark`, and a correct producer stops until the `'drain'` event. `pipe`/`pipeline` handle this automatically — which is precisely why hand-rolling the loop is a mistake. Ignore it and a fast reader feeding a slow writer buffers the whole file in memory, defeating the point of streaming.

**The four kinds**: **Readable** (source), **Writable** (sink), **Duplex** (both, independently — a TCP socket), **Transform** (a duplex whose output is a function of its input — gzip, a cipher, a CSV parser).

**Modes**: object mode passes arbitrary values instead of buffers, which makes streams a general pipeline abstraction; and a readable stream is async-iterable, so `for await (const chunk of stream)` is often more readable than events and gets backpressure right by construction.

**Where they fit in a hybrid stack**: streaming is how a Node front end handles large payloads without stalling the loop ([JS-014](#question-js-014)), and it is the same producer/consumer-with-backpressure design as a native feed handler — bounded buffer, flow control, incremental processing.

[↑ Back to question index](#question-index)
