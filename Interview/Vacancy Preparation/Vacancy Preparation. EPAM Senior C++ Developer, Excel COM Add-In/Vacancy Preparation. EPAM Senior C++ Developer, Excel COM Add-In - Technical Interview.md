# EPAM Senior C++ Developer - Technical Interview Questions and Answers

> Companion to the [main vacancy preparation](./Vacancy%20Preparation.%20EPAM%20Senior%20C%2B%2B%20Developer%2C%20Excel%20COM%20Add-In.md). This file holds the detailed technical question bank and prepared answers; the main file owns positioning, career evidence, stories, priorities and questions for the employer. Prepared knowledge here must not be presented as past production experience.

Position focus: **Senior C++ Developer, C++ + JavaScript/Node.js, Microsoft Excel COM Add-In, Windows, performance, multithreading**

---

# 1. Modern C++

## 1. What is the Rule of 3 / 5 / 0?

**Answer**

Rule of 3: if a class manually defines one of:

- destructor
- copy constructor
- copy assignment operator

it usually needs all three.

Rule of 5 extends this with:

- move constructor
- move assignment operator

Rule of 0 means that ideally resource ownership is delegated to RAII types such as `std::vector`, `std::string`, `std::unique_ptr`, so the class does not need to manually implement any special member functions.

**Senior point:** Rule of 0 is generally preferable because it reduces ownership bugs and makes exception safety easier.

---

## 2. What does `std::move` actually do?

**Answer**

`std::move` does not move anything.

It is essentially a cast that converts an expression into an xvalue, allowing move-enabled overloads to be selected.

Conceptually:

```cpp
template<class T>
std::remove_reference_t<T>&& move(T&& t) noexcept {
    return static_cast<std::remove_reference_t<T>&&>(t);
}
```

The actual resource transfer happens inside the move constructor or move assignment operator.

---

## 3. What is the difference between lvalue and rvalue?

**Answer**

An lvalue represents an object with identity and a stable location.

An rvalue is typically a temporary value or an object whose resources may be reused.

Examples:

```cpp
int x = 10;   // x is an lvalue
int y = x;    // x is still an lvalue

int z = 10;   // literal 10 is a prvalue
```

`std::move(x)` produces an xvalue.

---

## 4. What are prvalue, xvalue and glvalue?

**Answer**

Modern C++ value categories can be viewed like this:

```text
expression
├── glvalue
│   ├── lvalue
│   └── xvalue
└── rvalue
    ├── prvalue
    └── xvalue
```

- **lvalue** — has identity and is not expiring
- **xvalue** — has identity but may be moved from
- **prvalue** — pure value, often used to initialize an object
- **glvalue** — expression with identity
- **rvalue** — prvalue or xvalue

---

## 5. What is a forwarding reference?

**Answer**

A forwarding reference is `T&&` where `T` is a deduced template parameter.

Example:

```cpp
template<class T>
void foo(T&& value);
```

It can bind to both lvalues and rvalues.

Reference collapsing rules determine the final type:

```text
T&  + &  -> T&
T&  + && -> T&
T&& + &  -> T&
T&& + && -> T&&
```

---

## 6. What is `std::forward` used for?

**Answer**

`std::forward<T>` preserves the original value category of an argument.

Example:

```cpp
template<class T>
void wrapper(T&& value) {
    process(std::forward<T>(value));
}
```

Without `std::forward`, the named variable `value` is always an lvalue inside the function.

---

## 7. Why should move constructors often be `noexcept`?

**Answer**

Standard containers such as `std::vector` try to provide strong exception guarantees.

During reallocation, `vector` may prefer copying instead of moving if the move constructor can throw.

So:

```cpp
T(T&&) noexcept;
```

can allow `vector` to move elements efficiently.

---

## 8. When does the compiler generate a move constructor?

**Answer**

The exact rules depend on which special member functions are user-declared.

A useful interview-level rule:

If you explicitly declare certain special members such as a destructor, copy constructor, or copy assignment operator, implicit move generation may be suppressed.

Therefore, when manually managing ownership, think in terms of the Rule of 5.

---

## 9. What is copy elision?

**Answer**

Copy elision allows construction directly in the final destination instead of constructing a temporary and copying/moving it.

Example:

```cpp
T make() {
    return T{};
}
```

Since C++17, some cases are guaranteed and no move/copy constructor is required.

NRVO is a related optimization for named local variables:

```cpp
T make() {
    T t;
    return t;
}
```

NRVO itself is not guaranteed in all cases.

---

## 10. Why can `const T&` bind to a temporary?

**Answer**

C++ allows a const lvalue reference to bind to a temporary and extend the temporary's lifetime.

Example:

```cpp
const std::string& s = std::string("hello");
```

The temporary lives as long as `s`.

This is a language rule, not related to where `const` objects are stored in memory.

---

## 11. When does lifetime extension not work?

**Answer**

Lifetime extension is not transitive.

Example:

```cpp
const T& getRef() {
    return T{};
}
```

This returns a dangling reference.

Also, storing a reference member initialized from a temporary can be dangerous depending on context.

The key point is that lifetime extension only happens in specific language-defined binding contexts.

---

## 12. What is the difference between `auto` and `decltype(auto)`?

**Answer**

`auto` follows template-like deduction and usually drops references and top-level `const`.

`decltype(auto)` uses `decltype` rules and preserves the exact type category.

Example:

```cpp
int x = 10;
int& ref = x;

auto a = ref;          // int
decltype(auto) b = ref; // int&
```

---

## 13. What is `decltype`?

**Answer**

`decltype(expr)` determines the type of an expression.

Important rule:

```cpp
decltype(x)
```

for an unparenthesized variable gives its declared type.

But:

```cpp
decltype((x))
```

uses expression category rules and usually gives `T&` for an lvalue.

---

## 14. What is `explicit` used for?

**Answer**

`explicit` prevents unwanted implicit conversions through constructors or conversion operators.

Example:

```cpp
struct Meter {
    explicit Meter(double value);
};
```

This prevents:

```cpp
Meter m = 10.0;
```

but allows:

```cpp
Meter m(10.0);
```

---

## 15. What do `= default` and `= delete` mean?

**Answer**

`= default` asks the compiler to generate the normal implementation.

```cpp
Foo(const Foo&) = default;
```

`= delete` makes a function unavailable.

```cpp
Foo(const Foo&) = delete;
```

Useful for preventing copying or unwanted overloads.

---

## 16. What is undefined behavior?

**Answer**

Undefined behavior means the C++ standard imposes no requirements on what happens.

Examples:

- dereferencing a null pointer
- signed integer overflow
- accessing an object after lifetime ended
- data race
- out-of-bounds access

Compilers may optimize under the assumption that UB never happens.

---

## 17. Undefined vs unspecified vs implementation-defined behavior?

**Answer**

- **Undefined behavior** — no requirements
- **Unspecified behavior** — one of several valid behaviors, implementation does not need to document which
- **Implementation-defined behavior** — implementation chooses and documents behavior

Example of implementation-defined behavior: exact size of some integer types.

---

## 18. What is the as-if rule?

**Answer**

The compiler may transform code however it wants as long as observable behavior remains equivalent according to the language rules.

This allows aggressive optimization.

---

## 19. What is `volatile` for?

**Answer**

`volatile` tells the compiler that an object may change for reasons outside normal program flow.

Typical use cases:

- memory-mapped hardware registers
- some low-level embedded interactions

It is **not** a synchronization primitive and does not make code thread-safe.

Use `std::atomic`, mutexes, or other synchronization tools for concurrency.

---

## 20. Can you add things to namespace `std`?

**Answer**

In general, no.

Adding declarations to `std` is undefined behavior unless the standard explicitly permits it.

One notable historical exception is specializing certain standard templates for user-defined types where the standard allows it, for example some `std::hash` specializations.

You should not add new overloads of standard functions to `std`.

---

# 2. Object Model and OOP

## 21. Why does a base class often need a virtual destructor?

**Answer**

If an object may be destroyed through a base pointer, the base destructor must be virtual.

```cpp
Base* p = new Derived;
delete p;
```

Without a virtual destructor, behavior is undefined because the derived destructor may not run correctly.

---

## 22. How do virtual functions usually work?

**Answer**

Typical implementations use:

- a hidden pointer in each polymorphic object: `vptr`
- a virtual function table: `vtable`

The object points to the table for its dynamic type.

Virtual calls use the table to select the final override at runtime.

This is an implementation strategy, not something mandated exactly by the C++ standard.

---

## 23. Is there a vtable per object?

**Answer**

Usually no.

Objects typically contain a `vptr`, while the `vtable` is shared between objects of the same dynamic type.

---

## 24. What happens when calling a virtual function from a constructor or destructor?

**Answer**

Dynamic dispatch is limited to the class currently being constructed or destroyed.

During `Base` construction, a virtual call resolves to `Base`, not a future `Derived` override.

Similarly during destruction, derived parts may already be gone.

---

## 25. What is object slicing?

**Answer**

Object slicing occurs when a derived object is copied into a base object by value.

```cpp
Derived d;
Base b = d;
```

The derived-specific part is lost.

Use references or pointers for polymorphic behavior.

---

## 26. Overloading vs overriding?

**Answer**

**Overloading**: same function name with different parameter lists, usually resolved at compile time.

**Overriding**: derived class provides a new implementation of a virtual function from a base class.

Use `override` to let the compiler verify correctness.

---

## 27. What is virtual inheritance?

**Answer**

Virtual inheritance solves duplicate base subobjects in diamond inheritance.

Without virtual inheritance:

```text
    A
   / \
  B   C
   \ /
    D
```

`D` can contain two separate `A` subobjects.

With virtual inheritance, `B` and `C` share one `A` subobject.

---

## 28. What determines the size of a C++ class?

**Answer**

Factors include:

- data members
- alignment
- padding
- base classes
- potentially a `vptr`
- empty base optimization
- platform ABI

An empty class normally has size at least 1 so distinct objects can have distinct addresses.

---

# 3. RAII and Smart Pointers

## 29. What is RAII?

**Answer**

Resource Acquisition Is Initialization means resource ownership is tied to object lifetime.

Acquire in construction, release in destruction.

Examples:

- `std::unique_ptr`
- `std::vector`
- `std::lock_guard`
- file/socket wrapper classes

RAII makes cleanup deterministic and naturally integrates with exceptions.

---

## 30. How does `std::unique_ptr` work?

**Answer**

`unique_ptr` has exclusive ownership of an object.

It:

- cannot be copied
- can be moved
- destroys the owned object automatically
- may use a custom deleter

Ownership transfer is explicit through `std::move`.

---

## 31. How should `unique_ptr` be passed to a function?

**Answer**

Depends on semantics.

```cpp
void take(std::unique_ptr<T> p);
```

means ownership is transferred.

```cpp
void observe(const T& t);
```

means no ownership transfer.

```cpp
void maybeObserve(const T* p);
```

means non-owning and nullable.

Avoid passing `unique_ptr&` unless the function specifically needs to modify or replace the smart pointer itself.

---

## 32. How does `shared_ptr` work?

**Answer**

`shared_ptr` typically refers to a control block containing:

- strong reference count
- weak reference count
- deleter
- allocator-related metadata

The managed object is destroyed when the strong count reaches zero.

The control block survives until weak references are also gone.

---

## 33. What is the difference between `make_shared<T>()` and `shared_ptr<T>(new T)`?

**Answer**

`make_shared` can often allocate:

- object
- control block

in one allocation.

That improves locality and reduces allocation overhead.

A caveat: when `weak_ptr`s remain, memory containing the combined allocation may remain allocated even after the object itself is destroyed.

---

## 34. Why is this dangerous?

```cpp
T* p = new T;
std::shared_ptr<T> a(p);
std::shared_ptr<T> b(p);
```

**Answer**

`a` and `b` have separate control blocks.

Each believes it owns `p`.

Both eventually attempt to delete the same object, causing double deletion / undefined behavior.

---

## 35. Why are cyclic `shared_ptr` references a problem?

**Answer**

Example:

```text
A -> shared_ptr<B>
B -> shared_ptr<A>
```

Reference counts never reach zero.

The objects leak.

Usually one side should use `std::weak_ptr`.

---

## 36. What does `weak_ptr::lock()` do?

**Answer**

It attempts to create a `shared_ptr` from a `weak_ptr`.

If the object still exists, it returns a valid `shared_ptr`.

Otherwise, it returns an empty `shared_ptr`.

---

## 37. What is `enable_shared_from_this`?

**Answer**

It allows an object already owned by `shared_ptr` to safely obtain another `shared_ptr` sharing the same control block.

Without it, doing:

```cpp
std::shared_ptr<T>(this)
```

would create another unrelated control block and can cause double deletion.

---

## 38. Is `shared_ptr` thread-safe?

**Answer**

The control block reference counting operations are thread-safe across different `shared_ptr` instances.

But the managed object itself is not automatically thread-safe.

Two threads can safely copy/destroy separate `shared_ptr`s referring to the same object, but concurrent unsynchronized mutation of the object still causes a data race.

---

# 4. STL and Data Structures

## 39. How does `std::vector` work?

**Answer**

`vector` stores elements contiguously.

It tracks roughly:

- pointer to storage
- size
- capacity

When size exceeds capacity, it allocates a larger block and moves or copies existing elements.

---

## 40. What is the difference between `size()` and `capacity()`?

**Answer**

`size()` = number of constructed elements.

`capacity()` = number of elements that fit before another reallocation is required.

---

## 41. `reserve()` vs `resize()`?

**Answer**

`reserve(n)` changes capacity but does not change logical size.

`resize(n)` changes the number of actual elements.

---

## 42. When are vector iterators invalidated?

**Answer**

A reallocation invalidates:

- iterators
- pointers
- references

to all elements.

Without reallocation, insertion/erase may invalidate only positions at or after the modified location depending on operation.

---

## 43. Why is `push_back` amortized O(1)?

**Answer**

Most pushes are constant time.

Occasionally `vector` reallocates and moves O(n) elements.

Because capacity typically grows geometrically, the total cost of many insertions is linear, so average amortized cost per insertion is O(1).

---

## 44. Why is `vector` often faster than `list`?

**Answer**

Despite `list` having O(1) insertion at a known position, `vector` benefits from:

- contiguous memory
- better cache locality
- fewer allocations
- hardware prefetching
- lower pointer chasing overhead

Modern CPU behavior often dominates theoretical operation counts.

---

## 45. `map` vs `unordered_map`?

**Answer**

`std::map`:

- typically balanced tree
- ordered keys
- O(log n) operations

`std::unordered_map`:

- hash table
- average O(1)
- worst-case O(n)
- no ordering guarantee

Choice depends on ordering needs, key type, hash quality, memory usage and access pattern.

---

## 46. Why can `unordered_map` become O(n)?

**Answer**

Many keys may end up in the same bucket because of poor hashing or adversarial input.

Then lookup may degrade toward linear traversal.

---

## 47. What is rehashing?

**Answer**

When load factor grows beyond a threshold, an unordered container may create a larger bucket array and redistribute elements.

Rehashing can invalidate iterators and is O(n).

---

## 48. What is strict weak ordering?

**Answer**

Comparators used by ordered STL algorithms/containers must behave like a consistent ordering.

Properties include:

- irreflexive
- transitive
- consistent equivalence relation

Incorrect comparators can make algorithms behave unpredictably and may violate library preconditions.

---

## 49. `push_back` vs `emplace_back`?

**Answer**

`push_back` inserts an already-created value.

`emplace_back` constructs the object directly in container storage from constructor arguments.

`emplace_back` is not automatically faster.

If you already have a `T`, `push_back(std::move(t))` can be equally good.

---

## 50. What is `std::string_view`?

**Answer**

A non-owning view over character data.

It avoids copying, but lifetime must be handled carefully.

Dangerous:

```cpp
std::string_view f() {
    return std::string("abc");
}
```

The returned view dangles.

---

## 51. What is `std::span`?

**Answer**

A non-owning view over contiguous elements.

It can represent arrays, vectors and other contiguous storage without copying.

Useful for API boundaries when ownership stays elsewhere.

---

# 5. Multithreading and Memory Model

## 52. What is a data race?

**Answer**

A data race occurs when:

- two or more threads access the same memory location
- at least one access is a write
- accesses are not properly synchronized

In C++, a data race causes undefined behavior.

---

## 53. Race condition vs data race?

**Answer**

A **race condition** is a broader logical problem where behavior depends on timing/order.

A **data race** has a precise C++ memory-model definition involving unsynchronized memory accesses.

You can have a race condition without a data race.

---

## 54. What does a mutex provide?

**Answer**

A mutex provides mutual exclusion and synchronization.

It ensures only one protected critical section executes at a time and establishes memory-order relationships between unlock and subsequent lock operations.

---

## 55. `lock_guard` vs `unique_lock`?

**Answer**

`std::lock_guard`:

- simple RAII lock
- locks immediately
- cannot manually unlock/relock

`std::unique_lock`:

- more flexible
- can defer locking
- unlock/relock
- required by `condition_variable`

It has slightly more overhead because it stores additional state.

---

## 56. What is `scoped_lock`?

**Answer**

`std::scoped_lock` can lock one or several mutexes using deadlock-avoidance mechanisms.

Example:

```cpp
std::scoped_lock lock(m1, m2);
```

Useful when multiple mutexes must be acquired together.

---

## 57. What causes deadlock?

**Answer**

Classic Coffman conditions:

1. mutual exclusion
2. hold and wait
3. no preemption
4. circular wait

Typical prevention techniques:

- consistent lock order
- `std::scoped_lock`
- smaller critical sections
- avoiding nested locking

---

## 58. What is a condition variable?

**Answer**

A condition variable allows threads to sleep until some condition becomes true.

Example:

```cpp
std::unique_lock lock(m);
cv.wait(lock, [&] { return !queue.empty(); });
```

The mutex protects the condition state.

---

## 59. Why must condition variables use a predicate?

**Answer**

Because wakeups can be spurious and notifications may happen before a thread actually starts waiting.

The predicate checks the real condition under the mutex.

---

## 60. What is `std::atomic`?

**Answer**

`std::atomic<T>` provides operations that are indivisible with respect to other threads and participate in the C++ memory model.

It can be used for counters, flags and lock-free algorithms.

Atomicity alone does not solve every synchronization problem.

---

## 61. Atomic vs mutex?

**Answer**

Atomics are excellent for simple independent state transitions.

Mutexes are better when several values must be updated consistently or an invariant spans multiple operations.

A lock-free solution is not automatically faster or simpler.

---

## 62. What is compare-and-swap?

**Answer**

CAS compares an atomic value with an expected value and replaces it only if they match.

C++ provides:

```cpp
compare_exchange_weak
compare_exchange_strong
```

It is a fundamental building block for many lock-free algorithms.

---

## 63. `compare_exchange_weak` vs `strong`?

**Answer**

`weak` may fail spuriously even if the values match.

It is commonly used inside retry loops and can map more efficiently to some hardware.

`strong` does not allow this spurious-failure behavior.

---

## 64. What is `memory_order_relaxed`?

**Answer**

It guarantees atomicity of the operation but provides no synchronization ordering with other memory operations.

Useful for things like independent statistics counters.

---

## 65. What are acquire and release semantics?

**Answer**

A release store and an acquire load that observes it can establish synchronization.

Conceptually:

Thread A:

```cpp
data = 42;
ready.store(true, std::memory_order_release);
```

Thread B:

```cpp
if (ready.load(std::memory_order_acquire)) {
    use(data);
}
```

If B observes the released value, writes before the release become visible to B after the acquire.

---

## 66. What is happens-before?

**Answer**

A happens-before relationship guarantees ordering and visibility between operations in the C++ memory model.

If write A happens-before read B, B is guaranteed to observe effects consistent with that ordering.

---

## 67. What is sequential consistency?

**Answer**

`memory_order_seq_cst` is the strongest standard memory ordering.

Operations behave as if they participate in one global order consistent with each thread's program order.

It is easiest to reason about but may impose more constraints on optimization/hardware.

---

## 68. What is false sharing?

**Answer**

False sharing occurs when threads modify different variables that happen to reside on the same cache line.

The variables are logically independent, but cache coherence causes the cache line to bounce between cores.

This can severely reduce performance.

---

## 69. How would you implement producer-consumer?

**Answer**

Typical design:

```text
producer threads
      ↓
thread-safe queue
      ↓
consumer thread(s)
```

Use:

- mutex
- condition variable
- queue
- shutdown flag

For very high throughput, consider batching, bounded queues and backpressure.

---

## 70. How would you make a thread-safe queue?

**Answer**

Basic version:

- mutex protects queue
- `condition_variable` waits for non-empty state
- `push()` locks, inserts, notifies
- `pop()` waits with predicate, removes under lock
- define shutdown semantics

For production code also consider:

- bounded capacity
- cancellation
- move-only values
- exception safety

---

# 6. COM Fundamentals

## 71. What is COM?

**Answer**

COM is Microsoft's binary component model for interoperable software components.

It defines:

- binary interface conventions
- interface discovery
- lifetime management
- activation
- marshaling between threads/processes

It allows components compiled with different languages/tools to interact through stable ABI-level interfaces.

---

## 72. What is `IUnknown`?

**Answer**

The fundamental COM interface.

It exposes:

```cpp
QueryInterface
AddRef
Release
```

Every COM interface ultimately derives from the `IUnknown` contract.

---

## 73. What does `QueryInterface` do?

**Answer**

It asks a COM object whether it supports a specific interface identified by IID.

If supported, it returns an interface pointer and increments its reference count.

It enables interface discovery without depending on concrete object types.

---

## 74. What do `AddRef` and `Release` do?

**Answer**

They implement COM reference counting.

- `AddRef()` increments reference count
- `Release()` decrements it
- object normally destroys itself when the count reaches zero

Correct ownership discipline is essential.

---

## 75. What is a GUID / IID / CLSID?

**Answer**

A GUID is a globally unique identifier.

Common COM uses:

- **IID** — identifies an interface
- **CLSID** — identifies a COM class

These identifiers allow binary components to refer to interfaces/classes without relying on C++ names.

---

## 76. What is `CoCreateInstance`?

**Answer**

It asks COM to create an instance of a registered COM class.

Inputs include:

- CLSID
- activation context
- requested IID

COM locates and activates the corresponding server and returns the requested interface.

---

## 77. In-process vs out-of-process COM server?

**Answer**

**In-process server**

Usually a DLL loaded into the client process.

Pros:

- lower call overhead

Cons:

- crash can take down host process

**Out-of-process server**

Usually an EXE.

Pros:

- isolation

Cons:

- marshaling and IPC overhead

---

## 78. What is a COM class factory?

**Answer**

A class factory creates COM objects.

The standard interface is `IClassFactory`.

COM may obtain a class factory and ask it to create instances of a requested COM class.

---

## 79. What is `HRESULT`?

**Answer**

A 32-bit status code commonly returned by COM APIs.

Use helpers/macros such as:

```cpp
SUCCEEDED(hr)
FAILED(hr)
```

A negative severity bit generally indicates failure.

---

## 80. What is `BSTR`?

**Answer**

A COM string type used by Automation.

It is length-prefixed and normally allocated/freed with COM/OLE functions such as:

```cpp
SysAllocString
SysFreeString
```

It is not just a raw null-terminated `wchar_t*`.

---

## 81. What is `VARIANT`?

**Answer**

A tagged union used by COM Automation to represent values of different runtime types.

It can hold values such as:

- integers
- doubles
- BSTR
- COM interface pointers
- arrays
- empty/null states

Its lifetime must be managed correctly, commonly with `VariantInit` and `VariantClear`.

---

## 82. What is `SAFEARRAY`?

**Answer**

A COM-managed array representation that stores metadata such as:

- element type
- dimensions
- bounds

Frequently used together with `VARIANT` for Automation and Excel range data.

---

## 83. What is `IDispatch`?

**Answer**

`IDispatch` supports late-bound Automation.

A client can:

- resolve method/property names to DISPIDs
- invoke methods/properties dynamically

It is widely used in Office Automation.

---

## 84. Early binding vs late binding?

**Answer**

**Early binding**

Compiler knows interface/type information.

Pros:

- type safety
- faster calls
- compile-time checking

**Late binding**

Typically through `IDispatch`.

Pros:

- dynamic flexibility

Cons:

- runtime lookup
- weaker type checking
- more overhead

---

# 7. COM Apartments and Threading

## 85. What is a COM apartment?

**Answer**

An apartment defines COM's threading and synchronization model for objects and threads.

Common models:

- STA — Single-Threaded Apartment
- MTA — Multi-Threaded Apartment

A thread joins an apartment when COM is initialized on that thread.

---

## 86. `CoInitialize` vs `CoInitializeEx`?

**Answer**

`CoInitialize` initializes COM in STA mode.

`CoInitializeEx` lets you choose apartment model explicitly, for example:

```cpp
CoInitializeEx(nullptr, COINIT_APARTMENTTHREADED);
```

or:

```cpp
CoInitializeEx(nullptr, COINIT_MULTITHREADED);
```

Each thread using COM must initialize it appropriately.

---

## 87. What is STA?

**Answer**

In an STA, COM ensures calls into apartment-bound objects are serialized onto the apartment's owning thread.

STA often relies on a Windows message loop for call dispatch.

Office applications commonly expose automation objects associated with STA behavior.

---

## 88. What is MTA?

**Answer**

In an MTA, multiple threads can receive COM calls concurrently.

Objects used there must be designed for concurrency.

MTA avoids some STA dispatch constraints but requires thread-safe components.

---

## 89. Can you pass a COM interface pointer directly to another thread?

**Answer**

Not safely in the general case.

COM interface pointers are subject to apartment rules.

When crossing apartment boundaries, the interface may need to be marshaled so COM can provide an appropriate proxy.

---

## 90. What is COM marshaling?

**Answer**

Marshaling converts an interface reference into a representation usable across apartment or process boundaries.

COM may create proxies/stubs so calls can safely cross those boundaries.

---

## 91. Why can using Excel COM objects from worker threads be problematic?

**Answer**

Excel's object model is heavily tied to its main/UI apartment.

Calling it from arbitrary worker threads can cause:

- marshaling overhead
- reentrancy issues
- blocking
- invalid apartment access patterns
- difficult deadlocks

A common architecture is:

```text
worker threads
    ↓
queue / batch
    ↓
Excel/UI/COM thread
```

---

## 92. Why does an STA usually need a message pump?

**Answer**

COM may dispatch cross-apartment calls through Windows messages.

If the STA thread stops pumping messages, COM calls can stall or deadlock.

This is particularly important for UI applications and Office automation.

---

# 8. Excel Add-In / Office Integration

## 93. What is a COM Add-In?

**Answer**

A COM Add-In is a COM component loaded by an Office application.

It can integrate with Excel lifecycle and object model.

Historically, Office COM Add-Ins often use interfaces such as `IDTExtensibility2`.

---

## 94. What is `IDTExtensibility2`?

**Answer**

A classic Office extensibility interface used for add-in lifecycle callbacks such as:

- connection
- startup complete
- disconnection
- add-in updates

The exact architecture depends on the add-in technology used.

---

## 95. What are important Excel Object Model objects?

**Answer**

Common hierarchy:

```text
Application
  ↓
Workbooks
  ↓
Workbook
  ↓
Worksheets
  ↓
Worksheet
  ↓
Range
```

`Range` is especially important for reading and writing cell blocks efficiently.

---

## 96. Why is reading cells one-by-one through COM slow?

**Answer**

Every COM property/method call has overhead.

Doing:

```text
1,000,000 cells
×
1 COM call per cell
```

can be dramatically slower than fetching one large `Range`.

The usual optimization is batching.

---

## 97. How would you efficiently read a large Excel range?

**Answer**

Prefer:

```text
one Range request
↓
VARIANT / SAFEARRAY
↓
process locally in C++
```

instead of one COM call per cell.

This reduces boundary crossings drastically.

---

## 98. How would you update many Excel cells efficiently?

**Answer**

Use:

- coalescing
- batching
- range writes
- reduced update frequency
- minimal COM round-trips
- avoid unnecessary recalculation/redraw when possible

For real-time feeds, intermediate values often do not need to be displayed individually.

---

# 9. JavaScript Fundamentals

## 99. `var` vs `let` vs `const`?

**Answer**

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

---

## 100. What is a closure?

**Answer**

A closure is a function together with access to variables from its lexical scope even after the outer function has returned.

Example:

```js
function counter() {
    let value = 0;

    return () => ++value;
}
```

The returned function keeps access to `value`.

---

## 101. What is hoisting?

**Answer**

Declarations are processed before execution, but behavior differs.

Function declarations are available earlier.

`var` is hoisted and initialized to `undefined`.

`let` and `const` are hoisted but remain unavailable in the temporal dead zone until their declaration is executed.

---

## 102. What is the prototype chain?

**Answer**

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

---

## 103. How does `this` work in JavaScript?

**Answer**

For normal functions, `this` depends primarily on how the function is called.

Arrow functions do not bind their own `this`; they capture lexical `this` from the surrounding scope.

---

## 104. `==` vs `===`?

**Answer**

`==` performs type coercion.

`===` compares without implicit type conversion.

In most application code, `===` is safer and more predictable.

---

## 105. What is a Promise?

**Answer**

A Promise represents an asynchronous result.

States:

- pending
- fulfilled
- rejected

Handlers are registered with:

```js
.then(...)
.catch(...)
.finally(...)
```

---

## 106. How does `async/await` work?

**Answer**

An `async` function always returns a Promise.

`await` pauses execution of that async function until the awaited Promise settles, without blocking the entire JavaScript runtime thread.

Continuation is scheduled through the microtask mechanism.

---

## 107. What is the JavaScript event loop?

**Answer**

The event loop coordinates execution of:

- synchronous call stack
- task/macrotask queues
- microtask queue
- asynchronous I/O completions

JavaScript executes application code on one main thread, while runtime facilities can perform I/O or work elsewhere.

---

## 108. What does this print?

```js
console.log(1);

setTimeout(() => console.log(2), 0);

Promise.resolve().then(() => console.log(3));

console.log(4);
```

**Answer**

```text
1
4
3
2
```

Promise callbacks run as microtasks before the next timer/macrotask.

---

## 109. Is JavaScript really single-threaded?

**Answer**

Main JavaScript execution typically runs on one thread.

But runtimes such as browsers and Node.js use:

- OS async facilities
- worker threads
- thread pools
- background runtime components

The important point is that the main JS event loop can still be blocked by CPU-heavy synchronous code.

---

# 10. Node.js

## 110. What is the Node.js event loop?

**Answer**

Node.js executes JavaScript callbacks through an event loop.

Many I/O operations are asynchronous and are handled through OS mechanisms and libuv.

When results are ready, callbacks are queued for execution on the JavaScript thread.

---

## 111. What is libuv?

**Answer**

libuv is the cross-platform library used by Node.js for:

- event loop
- async I/O abstraction
- timers
- filesystem operations
- networking
- thread pool tasks

---

## 112. Why can CPU-heavy code be a problem in Node.js?

**Answer**

CPU-heavy synchronous code blocks the event loop.

While it runs, Node cannot process other JavaScript callbacks efficiently.

Solutions can include:

- Worker Threads
- child processes
- native C++ worker threads
- chunking/batching work

---

## 113. What are Worker Threads?

**Answer**

Worker Threads allow JavaScript to run in additional threads.

They are useful for CPU-bound work.

Communication can use messages or shared memory.

---

## 114. What are Node.js streams?

**Answer**

Streams process data incrementally instead of loading everything into memory at once.

Useful for:

- files
- network traffic
- large datasets
- real-time pipelines

Types include readable, writable, duplex and transform streams.

---

# 11. C++ ↔ JavaScript Integration

## 115. How would you connect a C++ backend with JavaScript?

**Answer**

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

---

## 116. JSON vs binary serialization?

**Answer**

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

---

## 117. How should errors cross the C++/JS boundary?

**Answer**

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

---

## 118. How do you avoid blocking the JS event loop with C++ work?

**Answer**

Do heavy work on:

- native worker threads
- thread pool
- worker process

Then post the result back asynchronously.

The JS-facing call should ideally start work and return quickly.

---

# 12. Performance

## 119. How do you investigate a performance problem?

**Answer**

Start with measurement, not assumptions.

Typical workflow:

```text
reproduce
↓
measure baseline
↓
profile CPU / memory / locks / I/O
↓
identify hot path
↓
optimize
↓
measure again
```

Always verify that the optimization changed the actual bottleneck.

---

## 120. Latency vs throughput?

**Answer**

**Latency** — time for one operation/request.

**Throughput** — number of operations completed per unit time.

Optimizations can improve one while hurting the other.

Batching often improves throughput but may increase latency.

---

## 121. What are P50, P95 and P99?

**Answer**

Percentiles describe latency distribution.

- P50 — median
- P95 — 95% of requests are faster than this
- P99 — 99% are faster than this

Tail latency is often more important than average latency in real-time systems.

---

## 122. What is batching?

**Answer**

Batching combines many small operations into fewer larger operations.

Benefits:

- fewer function calls
- fewer locks
- fewer COM crossings
- fewer syscalls
- better cache efficiency

Tradeoff: increased delay before the batch is processed.

---

## 123. What is throttling?

**Answer**

Throttling limits how frequently an action may execute.

Example:

```text
market updates: 20,000/sec
Excel refresh: 10/sec
```

The system may process incoming data continuously but refresh Excel only every 100 ms.

---

## 124. What is coalescing?

**Answer**

Coalescing combines multiple updates for the same logical item and keeps only the latest relevant state.

Example:

```text
AAPL: 100
AAPL: 101
AAPL: 102
```

Before Excel refresh, only `102` may matter.

---

## 125. What is backpressure?

**Answer**

Backpressure prevents a fast producer from overwhelming a slower consumer.

Techniques include:

- bounded queue
- dropping stale updates
- coalescing
- flow control
- slowing producer
- batch processing

---

## 126. Why can allocation be expensive?

**Answer**

Heap allocation may involve:

- allocator bookkeeping
- synchronization
- fragmentation
- cache misses

In hot paths, excessive small allocations can significantly hurt performance.

---

## 127. How do you reduce allocation overhead?

**Answer**

Possible techniques:

- reuse buffers
- reserve containers
- move instead of copy
- small-object optimization
- pooling where justified
- avoid temporary strings/objects
- process data in-place

Always profile first.

---

# 13. Windows / Debugging

## 128. Static vs dynamic library?

**Answer**

Static library code is linked into the executable at build time.

Dynamic libraries are loaded as separate modules, usually DLLs on Windows.

Dynamic libraries enable:

- shared binaries
- plugin architecture
- independent deployment

but introduce ABI/versioning concerns.

---

## 129. What are `LoadLibrary` and `GetProcAddress`?

**Answer**

`LoadLibrary` loads a DLL dynamically.

`GetProcAddress` obtains the address of an exported function by name or ordinal.

Useful for plugin systems and optional runtime dependencies.

---

## 130. Why is `DllMain` dangerous for complex work?

**Answer**

`DllMain` executes under the Windows loader lock.

Doing complex operations there can cause deadlocks or loader-related issues.

Avoid:

- thread creation patterns that wait
- loading additional DLLs
- COM initialization
- complex synchronization

Keep `DllMain` minimal.

---

## 131. How would you debug an Excel crash caused by your add-in?

**Answer**

Typical workflow:

1. reproduce if possible
2. obtain crash dump
3. load correct symbols/PDBs
4. inspect exception code
5. inspect crashing thread
6. inspect call stack
7. inspect object/lifetime state
8. check recent logs
9. verify heap corruption / race possibilities
10. reproduce under sanitizers or diagnostic builds where possible

---

## 132. How would you investigate a deadlock?

**Answer**

Capture a hang dump and inspect all thread stacks.

Look for:

- threads waiting on locks
- circular lock dependencies
- COM apartment waits
- UI thread blocked while another thread waits for UI
- inconsistent lock ordering

---

## 133. How would you debug a bug that appears only after several hours?

**Answer**

Suspect issues such as:

- leaks
- handle exhaustion
- reference-count leaks
- races
- accumulated queue backlog
- integer overflow
- stale pointers
- timer/lifetime bugs

Use:

- long-running telemetry
- counters
- memory graphs
- handle counts
- periodic dumps
- structured logs
- stress testing

---

# 14. Senior Architecture Scenarios

## 134. Excel freezes with 50,000 formulas. What do you do?

**Answer**

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

---

## 135. You receive 20,000 market updates per second but Excel only needs 10 refreshes per second. How would you design it?

**Answer**

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

---

## 136. A mutex fixed crashes but made the application much slower. What next?

**Answer**

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

---

## 137. JavaScript requests a large calculation from C++. How would you design it?

**Answer**

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

---

## 138. Excel COM call blocks while a background thread waits for Excel. What do you suspect?

**Answer**

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

---

## 139. What architecture would you use for C++ + Excel + Node.js?

**Answer**

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

---

# 15. Short Rapid-Fire Questions

## 140. `new` vs `malloc`?

**Answer**

`new` allocates memory and constructs the object.

`malloc` only allocates raw bytes.

`new` returns typed pointer and throws `std::bad_alloc` by default.

`malloc` returns `void*` and returns null on failure.

---

## 141. `delete` vs `delete[]`?

**Answer**

Use `delete` for objects allocated with scalar `new`.

Use `delete[]` for arrays allocated with `new[]`.

Mixing them is undefined behavior.

---

## 142. What is placement new?

**Answer**

Placement new constructs an object in already allocated storage.

```cpp
new (buffer) T(args...);
```

You must later call the destructor manually.

---

## 143. What is `std::terminate`?

**Answer**

The runtime calls `std::terminate` when exception handling cannot continue safely.

Examples:

- exception escapes a `noexcept` function
- destructor throws during stack unwinding and another exception is already active

---

## 144. What is stack unwinding?

**Answer**

When an exception propagates, local automatic objects in exited scopes are destroyed in reverse construction order.

RAII relies on this for cleanup.

---

## 145. Strong exception guarantee?

**Answer**

If an operation fails, program state remains unchanged.

Other common guarantees:

- basic guarantee — invariants preserved, no leaks
- no-throw guarantee — operation cannot fail by throwing

---

## 146. What is copy-and-swap?

**Answer**

A classic assignment technique:

1. copy into temporary
2. swap with current object
3. temporary destroys old state

It can provide strong exception safety, though move semantics and modern designs often reduce the need for manual use.

---

## 147. What is ODR?

**Answer**

ODR = One Definition Rule.

A program generally must have exactly one definition of entities requiring one, with special rules for inline functions/templates and equivalent definitions across translation units.

Violations can cause linker errors or undefined behavior.

---

## 148. What does `inline` really mean?

**Answer**

It allows identical definitions in multiple translation units under ODR rules.

It does **not** force machine-code inlining.

The optimizer decides whether to inline function calls.

---

## 149. What is ABI?

**Answer**

Application Binary Interface defines binary-level compatibility rules such as:

- calling conventions
- object layout
- name mangling
- register usage
- exception ABI
- vtable layout

ABI stability matters heavily across DLL/plugin boundaries.

---

## 150. What is name mangling?

**Answer**

The compiler encodes C++ function/type information into linker symbol names.

This supports overloading.

`extern "C"` disables C++ name mangling for compatible declarations and uses C linkage rules.

---

# 16. Final Revision Checklist

Before the interview, be able to explain these without hesitation:

- `std::move`
- `std::forward`
- Rule of 5 / Rule of 0
- `noexcept`
- lifetime extension
- `unique_ptr`
- `shared_ptr`
- `weak_ptr`
- `vector` reallocation
- `map` vs `unordered_map`
- data race
- mutex / condition variable
- atomics
- acquire/release
- false sharing
- producer-consumer
- `IUnknown`
- `QueryInterface`
- `AddRef` / `Release`
- `HRESULT`
- `VARIANT`
- `BSTR`
- `SAFEARRAY`
- `IDispatch`
- STA vs MTA
- COM marshaling
- Excel `Range`
- batching COM calls
- JS closures
- Promises
- async/await
- event loop
- Node.js event loop
- Worker Threads
- C++ ↔ JS async boundary
- batching
- throttling
- coalescing
- backpressure
- crash dumps
- deadlock analysis
- profiling before optimization

---

# 17. Highest-Priority Study Order

## Priority 1

- Modern C++
- ownership
- smart pointers
- STL
- `noexcept`
- object lifetime

## Priority 2

- multithreading
- memory model
- mutexes
- atomics
- condition variables
- producer-consumer

## Priority 3

- COM basics
- COM apartments
- Excel COM interactions
- batching calls

## Priority 4

- performance
- profiling
- cache locality
- contention
- batching / throttling / coalescing

## Priority 5

- JavaScript
- Promise
- event loop
- async/await
- Node.js

## Priority 6

- architecture scenarios
- C++ ↔ JS boundary
- debugging Excel add-ins
