# Software Design Assessment — Answer Guide

This handbook covers software-design principles, common architectural styles, and design patterns relevant to C++ engineers. Every question is followed by a model answer. Good interview answers should explain not only a definition, but also the forces, tradeoffs, failure modes, and circumstances in which a different design would be better.

Labels:

- **[Basic]** — expected core knowledge.
- **[Deep dive]** — tradeoffs, boundaries, and failure modes.
- **[Code]** — code reading or refactoring.
- **[Design]** — an open-ended system-design discussion.

## Contents

1. [SOLID](#1-solid)
2. [DRY, KISS, and YAGNI](#2-dry-kiss-and-yagni)
3. [ACID](#3-acid)
4. [Composition over inheritance](#4-composition-over-inheritance)
5. [Separating interface and implementation](#5-separating-interface-and-implementation)
6. [Singleton](#6-singleton)
7. [Factory Method](#7-factory-method)
8. [Adapter](#8-adapter)
9. [Observer](#9-observer)
10. [Strategy](#10-strategy)
11. [Iterator and its relationship to the STL](#11-iterator-and-its-relationship-to-the-stl)
12. [Other GoF patterns](#12-other-gof-patterns)
13. [Client-server architecture](#13-client-server-architecture)
14. [Producer-consumer](#14-producer-consumer)
15. [MVC](#15-mvc)
16. [Publish-subscribe and event-driven systems](#16-publish-subscribe-and-event-driven-systems)
17. [Naming and API consistency](#17-naming-and-api-consistency)
18. [Error handling](#18-error-handling)
19. [Compatibility, ABI stability, and library distribution](#19-compatibility-abi-stability-and-library-distribution)

---

# 1. SOLID

## 1.1. The principles as a set

1. **[Basic] What does SOLID stand for, and what problem is it trying to solve?**

   **Answer.** SOLID stands for Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, and Dependency Inversion. Together they are heuristics for keeping object-oriented code understandable and changeable: responsibilities have clear homes, variations occur behind stable boundaries, subtypes preserve contracts, clients depend only on what they use, and policy is not coupled to volatile details. They are not laws or a scoring system; applying them mechanically can create more indirection than the problem needs.

2. **[Deep dive] How do the five principles reinforce one another?**

   **Answer.** SRP helps identify cohesive components. ISP turns their public surfaces into focused contracts. LSP makes implementations safely interchangeable. OCP uses those stable contracts as extension points, and DIP keeps high-level policy dependent on the contracts rather than concrete infrastructure. A useful dependency direction is therefore from volatile details toward stable policy-owned abstractions.

   ```mermaid
   flowchart TB
       P[High-level policy] --> I[Stable focused interfaces]
       D1[Database detail] --> I
       D2[Network detail] --> I
       D3[Test double] --> I
       I --> C[Substitutable implementations]
   ```

3. **[Deep dive] What are common signs that SOLID has been over-applied?**

   **Answer.** Warning signs include an interface for every class despite having one stable implementation, factories that merely call constructors, deep chains of pass-through objects, one-method classes with no independent reason to change, and navigation across many files to understand a simple operation. Abstractions should isolate demonstrated volatility or a meaningful domain boundary. Speculative flexibility increases concepts, allocation, dispatch, build time, and maintenance cost.

## 1.2. Single Responsibility Principle

1. **[Basic] What does the Single Responsibility Principle actually mean?**

   **Answer.** A module should have one coherent reason to change—often phrased as responsibility to one actor or stakeholder—not literally one method or one task. Code that changes for tax policy should not be entangled with code that changes for database schema or report formatting. Cohesion and change coupling are better tests than class size alone.

2. **[Code] How would SRP improve a class that validates an order, calculates its price, stores it in SQL, and emails a receipt?**

   **Answer.** Keep the order and its domain invariants together, move pricing policy into a pricing service/strategy, persistence behind an order repository, and delivery behind a notification interface. An application service can coordinate the use case. The split is valuable because each part changes and is tested for a different reason; it should not be reduced to arbitrary “one function per class” fragmentation.

   ```cpp
   class CheckoutService {
   public:
       CheckoutService(PricingPolicy& pricing,
                       OrderRepository& orders,
                       ReceiptSender& receipts);

       CheckoutResult checkout(Order order);
   };
   ```

## 1.3. Open/Closed Principle

1. **[Basic] What does “open for extension, closed for modification” mean?**

   **Answer.** Stable, tested policy code should support an anticipated family of variations by adding a new implementation or configuration rather than editing a growing conditional in the core. “Closed” is relative, not absolute: defects and requirements still change existing code. The principle asks designers to place extension seams where change is recurrent and costly.

2. **[Deep dive] How can OCP be implemented without inheritance?**

   **Answer.** Function objects, templates, callbacks, `std::variant` visitation, data-driven tables, composition, and registration maps can all be extension mechanisms. Runtime subtype polymorphism is only one option. For a closed set of variants, a `variant` plus exhaustive visitor may be safer than an open hierarchy; for third-party plugins, a stable interface and factory may be appropriate.

## 1.4. Liskov Substitution Principle

1. **[Basic] What is the Liskov Substitution Principle?**

   **Answer.** Code written against a base abstraction should continue to behave correctly when given any subtype. A subtype must preserve the base contract: it must not strengthen preconditions, weaken promised postconditions, violate invariants, or introduce incompatible failure and lifetime behavior. Matching a C++ signature is necessary but not sufficient.

2. **[Code] Why is modeling `Square` as a mutable subtype of `Rectangle` often an LSP violation?**

   **Answer.** If `Rectangle` promises independently settable width and height, a `Square` must either break its equal-sides invariant or change both dimensions when one setter is called. A client that sets width and expects height unchanged no longer works. Prefer immutable shape values, a common `Shape` interface exposing only shared behavior such as `area()`, or separate unrelated types.

3. **[Deep dive] How do exceptions and performance relate to LSP?**

   **Answer.** A subtype that throws in situations the base promises to handle violates the behavioral contract, even though C++ has no checked-exception declaration. Extreme complexity changes can also violate practical substitutability—for example replacing an advertised constant-time operation with network I/O. Contracts should state relevant failure, blocking, complexity, thread-safety, and ownership expectations.

## 1.5. Interface Segregation Principle

1. **[Basic] What is the Interface Segregation Principle?**

   **Answer.** Clients should not depend on operations they do not use. Prefer cohesive role-based interfaces over one “fat” interface whose implementers must provide meaningless methods. The goal is client-specific contracts and lower change coupling, not the maximum possible number of tiny interfaces.

2. **[Code] How would you refactor an `IMachine` interface containing `print`, `scan`, `fax`, and `staple` when simple printers support only `print`?**

   **Answer.** Split capabilities such as `Printer`, `Scanner`, and `Fax`; let a multifunction device implement several. Algorithms request only the capability they need. A separate composed facade may still present the full device to UI code, but simple printers no longer throw “unsupported” from methods they were forced to implement.

## 1.6. Dependency Inversion Principle

1. **[Basic] What is the Dependency Inversion Principle?**

   **Answer.** High-level policy should not depend directly on low-level details; both should depend on abstractions, and abstractions should be shaped by policy rather than infrastructure. In source-code terms, dependency arrows point toward the stable business rules. A payment use case should depend on a policy-owned `PaymentGateway` contract, while a particular HTTP SDK adapter implements it.

2. **[Deep dive] Is Dependency Inversion the same as dependency injection?**

   **Answer.** No. DIP is an architectural dependency-direction principle. Dependency injection is a construction technique that supplies dependencies from outside through constructors, parameters, setters, or a container. DI often enables DIP and testing, but injecting a concrete low-level type does not invert the dependency, and DIP can be implemented with templates or factories without a DI framework.

3. **[Code] Improve code that constructs `SqlOrderRepository` inside `CheckoutService`.**

   **Answer.** Make the service depend on the smallest repository contract it needs and inject it, normally through the constructor. The composition root chooses the production adapter; tests can supply an in-memory implementation without changing business logic.

   ```cpp
   struct OrderRepository {
       virtual ~OrderRepository() = default;
       virtual void save(const Order&) = 0;
   };

   class CheckoutService {
   public:
       explicit CheckoutService(OrderRepository& repository)
           : repository_(repository) {}

   private:
       OrderRepository& repository_; // non-owning; composition root outlives service
   };
   ```

---

# 2. DRY, KISS, and YAGNI

1. **[Basic] Define DRY, KISS, and YAGNI.**

   **Answer.** DRY—Don't Repeat Yourself—asks for each piece of knowledge or policy to have one authoritative representation. KISS—Keep It Simple—prefers the simplest design that clearly satisfies current constraints. YAGNI—You Aren't Gonna Need It—avoids implementing speculative capability before a real requirement exists. All three reduce maintenance cost, but none means “write the fewest lines.”

2. **[Deep dive] Why is DRY about knowledge rather than identical text?**

   **Answer.** Two similar-looking blocks may represent independent business rules that happen to coincide today; merging them couples future changes incorrectly. Conversely, the same rule can be duplicated across code, validation schemas, documentation, and database constraints even when the text differs. Abstract only after identifying a shared reason to change.

3. **[Deep dive] When is duplication preferable to abstraction?**

   **Answer.** Small duplication is often cheaper when requirements are still diverging, when the shared abstraction would need flags and special cases, or when different owners/releases must evolve independently. The “rule of three” is a useful prompt: observe repeated cases before extracting the stable common concept. Duplication is evidence to investigate, not an automatic refactoring command.

4. **[Basic] What does simplicity mean under KISS?**

   **Answer.** Simplicity is low cognitive load and few interacting concepts while still meeting correctness, performance, security, and operability needs. A ten-line clever template can be less simple than a plain fifty-line state machine. Measure simplicity from the maintainer's perspective, including diagnostics, tests, failure recovery, and deployment—not only local line count.

5. **[Design] How do YAGNI and good extensibility coexist?**

   **Answer.** Implement today's use cases cleanly, preserve information, keep modules cohesive, and use reversible decisions at volatile boundaries. Do not build a plugin system, generalized rule engine, or distributed service solely for hypothetical use. Tests and clear seams make later refactoring safer; speculative abstractions can make it harder by locking in the wrong model.

---

# 3. ACID

1. **[Basic] What do the four ACID properties mean?**

   **Answer.** Atomicity means a transaction's changes commit as a unit or are rolled back. Consistency means a committed transaction preserves declared invariants; the application must still encode the correct invariants. Isolation defines how concurrent transactions' intermediate effects are hidden or controlled. Durability means a successful commit survives the failures covered by the storage system's contract.

2. **[Deep dive] Does ACID “consistency” mean all replicas immediately contain identical data?**

   **Answer.** No. In ACID, consistency concerns preservation of database constraints and application invariants across a transaction. Distributed-replica consistency is a separate topic involving models such as linearizability, sequential consistency, and eventual consistency. A system can provide ACID transactions on one node while replicas lag.

3. **[Deep dive] Which anomalies do isolation levels address?**

   **Answer.** Weak isolation can allow dirty reads, non-repeatable reads, phantom reads, lost updates, and write skew. Names such as Read Committed, Repeatable Read, Snapshot Isolation, and Serializable describe different guarantees, but exact behavior varies by database. Serializable aims to make committed outcomes equivalent to some serial execution; applications must handle abort/retry.

4. **[Design] How should transaction boundaries be chosen?**

   **Answer.** A transaction should encompass the smallest set of database changes that must preserve one invariant. Long transactions hold locks/versions, increase contention, and make retries expensive. Never keep a database transaction open while waiting for user input or an unreliable remote service; use state machines, reservations, idempotency, or sagas when one atomic database boundary cannot cover the workflow.

5. **[Code] Why is “commit the order, then publish an event” unsafe, and how does the transactional outbox help?**

   **Answer.** A crash after the database commit but before publication loses the event; publishing first can announce a transaction that later rolls back. With an outbox, the business row and an event record are written in one local transaction. A relay later publishes the outbox row and marks it processed; consumers must tolerate duplicate delivery through idempotency.

   ```mermaid
   sequenceDiagram
       participant App
       participant DB
       participant Relay
       participant Broker
       App->>DB: transaction: order + outbox event
       DB-->>App: commit
       Relay->>DB: read pending outbox rows
       Relay->>Broker: publish event
       Relay->>DB: mark delivered
   ```

---

# 4. Composition over inheritance

1. **[Basic] What does “favor composition over inheritance” mean?**

   **Answer.** Assemble behavior from owned or referenced collaborators rather than deriving merely to reuse implementation. Composition exposes dependencies, permits independent replacement, avoids fragile base-class coupling, and allows behavior to change at runtime. It is a preference, not a ban on inheritance.

2. **[Deep dive] When is public inheritance still the right choice?**

   **Answer.** Use it when there is a genuine behavioral subtype with a stable base contract, clients need substitution, and the base was designed for extension. Pure interfaces and framework-defined customization points are common examples. If the motivation is only access to protected helpers or code reuse, composition/delegation is usually safer.

3. **[Code] Replace inheritance used only to vary compression behavior.**

   **Answer.** Inject a strategy rather than deriving one exporter subclass per algorithm. The exporter retains its responsibility while compression varies independently.

   ```cpp
   struct Compressor {
       virtual ~Compressor() = default;
       virtual Bytes compress(ByteView input) const = 0;
   };

   class Exporter {
   public:
       explicit Exporter(std::unique_ptr<Compressor> compressor)
           : compressor_(std::move(compressor)) {}

       void export_data(ByteView input);

   private:
       std::unique_ptr<Compressor> compressor_;
   };
   ```

4. **[Deep dive] What are the costs of composition?**

   **Answer.** It may introduce forwarding code, more objects, explicit lifetime management, and runtime indirection when virtual interfaces/type erasure are used. Templates avoid runtime dispatch but can increase compile time and binary size. The benefit is localized coupling; choose value members, references, templates, or erased interfaces according to ownership and variability rather than defaulting to heap polymorphism.

---

# 5. Separating interface and implementation

1. **[Basic] What belongs in an interface, and what belongs in an implementation?**

   **Answer.** The interface communicates stable semantics: operations, types, ownership, lifetime, errors, thread safety, complexity, and invariants clients may rely on. The implementation contains algorithms, storage layout, third-party dependencies, caching, and other details clients should not depend upon. `private` alone does not fully hide a C++ implementation because private member layout and included types still appear in headers.

2. **[Deep dive] How does the PImpl idiom separate interface from implementation in C++?**

   **Answer.** The public class stores a pointer to an incomplete `Impl`; the `.cpp` defines `Impl` and the public operations. This hides representation and heavy dependencies, reduces recompilation, and can preserve class size/layout across compatible releases. It costs an indirection, usually an allocation, more explicit special-member handling, and weaker optimization across the boundary.

   ```cpp
   // widget.hpp
   class Widget {
   public:
       Widget();
       ~Widget();
       Widget(Widget&&) noexcept;
       Widget& operator=(Widget&&) noexcept;

       void refresh();

   private:
       struct Impl;
       std::unique_ptr<Impl> impl_;
   };
   ```

   Define the destructor and move operations in the `.cpp` after `Impl` is complete.

3. **[Deep dive] Is an abstract base class always the best interface?**

   **Answer.** No. A concrete value-type API, free functions, templates/concepts, function objects, or type erasure can provide a cleaner boundary. Runtime abstract interfaces are appropriate when implementations vary independently at runtime or across modules. Templates preserve static type information and optimize well but expose implementation and increase compile-time coupling.

4. **[Design] How does interface/implementation separation reduce build coupling?**

   **Answer.** Public headers include only types required to compile client code and use forward declarations where valid. Implementation-only headers stay in `.cpp` files, so changing a database SDK, algorithm, or private field does not rebuild every client. Stable boundary types, include-what-you-use discipline, PImpl, and modules can all help; excessive forward declarations must not obscure ownership or violate completeness requirements.

5. **[Deep dive] What must an interface document beyond function signatures?**

   **Answer.** State preconditions/postconditions, nullability, who owns returned resources, how long views/references remain valid, iterator invalidation, exception/error behavior, blocking and thread-safety rules, ordering, complexity, units, and versioning expectations. Undocumented semantics become accidental implementation dependencies and make compatible evolution much harder.

---

# 6. Singleton

1. **[Basic] What is the Singleton pattern?**

   **Answer.** Singleton ensures that a class has one instance within a defined scope and provides access to it. The scope must be explicit: one per process, thread, plugin, request, or logical context are different requirements. In practice the pattern often combines lifetime control with global access, and the global-access part causes most problems.

2. **[Code] What is a Meyers singleton, and what does C++ guarantee about it?**

   **Answer.** It is a function-local static instance. Since C++11, initialization is thread-safe: exactly one thread performs it and others wait. That guarantee does not make later mutable operations on the object thread-safe.

   ```cpp
   Service& service() {
       static Service instance;
       return instance;
   }
   ```

3. **[Deep dive] Why does Singleton make testing and dependency management harder?**

   **Answer.** Dependencies become hidden calls rather than constructor-visible requirements. Tests share mutable state, execution order matters, replacement with a fake is difficult, and parallel tests interfere. Passing an interface/reference from a composition root usually preserves the “one production instance” policy without making every consumer globally coupled to it.

4. **[Deep dive] Which lifetime and shutdown problems can a Singleton have?**

   **Answer.** Static objects in different translation units have difficult initialization/destruction ordering; one singleton may use another after it has been destroyed. Plugin unload, background threads, and callbacks make this worse. Prefer explicit application-lifetime ownership and shutdown order. Intentionally leaking a process-lifetime object can avoid destruction order but trades the issue for skipped cleanup and should be a deliberate platform-level decision.

5. **[Design] When can a Singleton-like design be acceptable?**

   **Answer.** It can be reasonable for immutable process-wide metadata, a narrowly controlled OS/runtime facility, or a composition-root-owned service whose uniqueness is enforced externally. Even then, consumers should generally receive a dependency rather than call a global accessor. The key question is whether uniqueness is a domain invariant or merely a convenient implementation assumption.

---

# 7. Factory Method

1. **[Basic] What is Factory Method, and how does it differ from a general factory function?**

   **Answer.** In the GoF pattern, a base “creator” defines an operation that relies on a product and exposes a virtual factory method that subclasses override to choose the concrete product. In everyday C++, “factory method” is often used more broadly for a named function that constructs an object while hiding its concrete type or complex validation. A static/free factory can be good design even when it is not the exact GoF pattern.

2. **[Code] Implement a factory that returns one of several polymorphic parsers.**

   **Answer.** Return `std::unique_ptr<Parser>` to express exclusive ownership and report unsupported formats explicitly.

   ```cpp
   enum class Format { json, xml };

   std::unique_ptr<Parser> make_parser(Format format) {
       switch (format) {
       case Format::json: return std::make_unique<JsonParser>();
       case Format::xml:  return std::make_unique<XmlParser>();
       }
       throw std::invalid_argument("unsupported format");
   }
   ```

   The base needs a virtual destructor. For expected configuration failures, return `std::expected<std::unique_ptr<Parser>, Error>` in C++23 instead of throwing.

3. **[Deep dive] When is a registry-based factory useful, and what are its risks?**

   **Answer.** A map from stable identifiers to creator callables allows plugins or separately developed modules to register products without modifying a central switch. Risks include static-initialization order, duplicate keys, non-deterministic registration, thread safety, hidden startup work, and dead stripping of self-registering object files. Explicit registration from the composition root is easier to reason about than global static registrars.

4. **[Deep dive] How does Factory Method relate to DIP and OCP?**

   **Answer.** It moves concrete construction out of policy code, so policy consumes a product abstraction while a composition layer chooses details. New product implementations can be added through a factory/registry seam. A factory does not automatically satisfy OCP: a central switch still changes for each product, which may be perfectly adequate for a small closed set.

---

# 8. Adapter

1. **[Basic] What problem does the Adapter pattern solve?**

   **Answer.** Adapter converts the interface of an existing component—the adaptee—into the interface a client expects. It preserves the client's model while containing translation of calls, data types, ownership, and errors at a boundary. It is especially useful around legacy code, third-party SDKs, operating-system APIs, and test doubles.

2. **[Code] Sketch an adapter from a legacy C API to a C++ storage interface.**

   **Answer.** The adapter owns or borrows the legacy handle with an explicit RAII policy and translates integer status codes into the application's error model.

   ```cpp
   class LegacyStorageAdapter final : public Storage {
   public:
       explicit LegacyStorageAdapter(LegacyHandle handle) : handle_(handle) {}

       Result<Data> load(Key key) override {
           LegacyBuffer buffer{};
           const int status = legacy_load(handle_, key.value(), &buffer);
           if (status != 0) return Error::from_legacy(status);
           return copy_and_release(buffer);
       }

   private:
       LegacyHandle handle_; // document ownership or wrap it in RAII
   };
   ```

3. **[Deep dive] How does Adapter differ from Facade, Decorator, and Proxy?**

   **Answer.** Adapter changes an interface to make incompatible types work together. Facade provides a simpler entry point to a subsystem. Decorator preserves an interface while adding behavior around another implementation. Proxy also preserves an interface but controls access, location, or lifecycle. The code shapes can look similar; intent and contract are the distinguishing features.

---

# 9. Observer

1. **[Basic] What are the roles in the Observer pattern?**

   **Answer.** A subject maintains subscriptions and notifies observers when relevant state/events change. Observers implement callbacks or provide callable objects. This creates one-to-many notification without the subject depending on concrete listeners, but the subject and observers still share a synchronous interaction/lifetime contract unless a broker or queue separates them.

2. **[Deep dive] Which lifetime problems must a C++ Observer implementation solve?**

   **Answer.** The subject must never call a destroyed observer, and an observer must be able to unsubscribe safely. Common solutions are RAII subscription tokens, connections owned by the subscriber, `weak_ptr` for shared-lifetime observers, or explicit ownership by the subject. Raw observer pointers are acceptable only with a rigorously documented lifetime relation. Avoid cycles when callbacks capture owners.

3. **[Deep dive] How should notification handle mutation, reentrancy, and concurrency?**

   **Answer.** A callback may unsubscribe itself, add another observer, publish recursively, or destroy related objects. Iterate over a stable snapshot or use a container/protocol designed for mutation, define whether new subscriptions see the current event, and avoid holding internal locks while invoking user code. Under concurrency, synchronize the subscription registry and specify whether unsubscribe waits for in-flight callbacks.

4. **[Code] What should an RAII subscription API look like?**

   **Answer.** `subscribe` should return a movable, noncopyable connection whose destructor disconnects. The connection must not contain an unsafe raw back-pointer if it can outlive the subject; shared connection state or a weak control block solves this.

   ```cpp
   class Subscription {
   public:
       Subscription(Subscription&&) noexcept = default;
       Subscription& operator=(Subscription&&) noexcept = default;
       Subscription(const Subscription&) = delete;
       Subscription& operator=(const Subscription&) = delete;
       ~Subscription(); // disconnects if the subject still exists
   };

   Subscription subscribe(std::function<void(const Event&)> callback);
   ```

---

# 10. Strategy

1. **[Basic] What is the Strategy pattern?**

   **Answer.** Strategy encapsulates interchangeable algorithms behind a common contract and lets a context delegate the varying behavior. It replaces conditionals or subclass explosions when the overall workflow is stable but one policy—pricing, retry, compression, routing—varies.

2. **[Deep dive] Compare runtime and compile-time strategies in C++.**

   **Answer.** A virtual interface or `std::function` supports runtime replacement and stable non-template clients but adds indirect calls and often lifetime management. A template policy has no required runtime dispatch and can inline aggressively, but fixes the strategy in the type, exposes implementation in headers, and can increase build time/code size. `std::variant` offers a closed runtime set with value semantics.

3. **[Code] How can a small stateless strategy be injected without an inheritance hierarchy?**

   **Answer.** Accept a callable by template or store an erased callable if it must vary later.

   ```cpp
   template<class Discount>
   Money total(const Cart& cart, Discount discount) {
       Money result = subtotal(cart);
       return result - std::invoke(discount, cart, result);
   }

   const Money final = total(cart, [](const Cart& c, Money subtotal) {
       return c.is_vip() ? subtotal * 0.10 : Money{};
   });
   ```

   Document whether the callable is copied, retained, allowed to throw, or invoked concurrently.

---

# 11. Iterator and its relationship to the STL

1. **[Basic] What problem does the Iterator pattern solve?**

   **Answer.** Iterator provides sequential or structured access to a collection without exposing its representation. The traversal state is separated from the container, so multiple traversals and generic algorithms are possible. In C++, iterators are value-like cursor abstractions integrated deeply into the standard library rather than usually implemented as a polymorphic GoF hierarchy.

2. **[Basic] How do STL algorithms use iterator pairs?**

   **Answer.** A half-open range `[first, last)` represents elements beginning at `first` and ending before `last`; an empty range has `first == last`. Algorithms use iterator operations rather than knowing the container type, which lets the same `std::find`, `std::sort`, or `std::copy` work across compatible containers. The iterator category/concept determines which algorithms and complexity guarantees are available.

3. **[Deep dive] What capabilities distinguish input, forward, bidirectional, random-access, and contiguous iterators?**

   **Answer.** Input iterators support single-pass reading. Forward iterators are multi-pass. Bidirectional iterators add decrement. Random-access iterators add constant-time jumps, distance, and ordering. Contiguous iterators additionally guarantee adjacent elements occupy adjacent memory and can expose an address through `std::to_address`. Output iterators model writing and have different readable/value requirements.

4. **[Deep dive] What is iterator invalidation, and why must it be part of a container's contract?**

   **Answer.** An iterator, pointer, or reference becomes invalid when an operation moves, erases, or destroys the element/storage it denotes. For example, `vector` reallocation invalidates all of them, while inserting into a node-based list usually preserves existing iterators. Using an invalid iterator is undefined behavior. APIs and reviews must consider the exact operation and container-specific invalidation rules.

5. **[Deep dive] What did ranges and sentinels add to the iterator model?**

   **Answer.** C++20 ranges bundle traversal endpoints, support composable lazy views, and use concepts for clearer constraints. The end sentinel need not have the same type as the iterator, which naturally models null-terminated or counted input. A view is usually non-owning or cheaply owning; its underlying range must remain valid, so lifetime remains a central design concern.

6. **[Code] What is required when designing a custom iterator for use with the STL?**

   **Answer.** Provide the operations and associated types required by the intended iterator concept, with correct value/reference semantics, equality, increment behavior, and complexity. Prefer checking with concepts such as `static_assert(std::forward_iterator<MyIterator>);`. Do not claim a stronger category than the implementation supports: generic algorithms rely on multi-pass and constant-time guarantees, not only syntax.

---

# 12. Other GoF patterns

## 12.1. Decorator

1. **[Basic] What is Decorator?**

   **Answer.** Decorator wraps an object behind the same interface and adds behavior before or after delegation. Examples include logging, metrics, caching, authorization, compression, and buffering. Decorators can be stacked without multiplying subclasses, but order may matter and the ownership of the wrapped component must be explicit.

2. **[Code] How might a logging decorator preserve the original storage contract?**

   **Answer.** It implements `Storage`, owns or borrows another `Storage`, logs metadata without changing results, and delegates. It must preserve exceptions, blocking semantics, and thread safety unless its contract explicitly says otherwise; accidentally swallowing errors would violate substitutability.

## 12.2. Facade

1. **[Basic] What is Facade?**

   **Answer.** Facade exposes a cohesive, simplified entry point over a complicated subsystem. It coordinates lower-level objects and prevents clients from depending on their arrangement. The subsystem can remain available for advanced use, but the facade defines the supported common path and can become an architectural boundary.

## 12.3. Proxy

1. **[Basic] What is Proxy, and which common variants exist?**

   **Answer.** Proxy stands in for another object while preserving its interface and controlling access. A remote proxy performs communication, a virtual proxy loads lazily, a protection proxy authorizes calls, and a smart/reference proxy manages lifetime or synchronization. Hidden remote I/O behind a local-looking interface can be misleading because latency and partial failure are not substitutable details; make important costs visible.

## 12.4. Command

1. **[Basic] What is Command?**

   **Answer.** Command represents a request as an object or callable, separating the requester from execution. It enables queuing, scheduling, retry, logging, macro composition, and sometimes undo. A command must define ownership of captured arguments and whether it can be repeated safely.

2. **[Deep dive] What is required for reliable undo and retry?**

   **Answer.** Undo needs either an inverse operation with saved prior state or a snapshot/event model; not every side effect is reversible. Retry needs idempotency or deduplication, especially after timeouts where the original result is unknown. A command that sends email or charges a card cannot simply be executed twice without an idempotency key and external-system cooperation.

## 12.5. Comparing wrapper-shaped patterns

1. **[Deep dive] Decorator, Adapter, Facade, and Proxy can all wrap objects. How do you identify the pattern?**

   **Answer.** Look at intent and contract. Adapter changes the interface; Decorator retains it and adds responsibility; Proxy retains it and controls access or location; Facade presents a new simplified interface to several subsystem parts. A class can serve more than one intent, but naming the dominant one clarifies which behavior clients may rely on.

---

# 13. Client-server architecture

1. **[Basic] What defines a client-server architecture?**

   **Answer.** Clients initiate requests for capabilities or resources provided by a server across a process or network boundary. The server centralizes a contract and may serve many clients concurrently. The boundary introduces serialization, authentication, latency, partial failure, independent deployment, and versioning concerns absent from ordinary in-process calls.

2. **[Deep dive] Compare stateful and stateless servers.**

   **Answer.** A stateless request carries all context needed to process it, making load balancing, failover, and horizontal scaling easier. A stateful server retains session or workflow state, which can reduce repeated data transfer and support richer protocols but requires affinity, replication, recovery, or externalized session storage. “Stateless” application servers still use durable shared state; it describes request/session handling, not the absence of databases.

3. **[Design] What belongs in a network API contract?**

   **Answer.** Define message schemas, operations, validation, authentication/authorization, timeouts, errors, idempotency, ordering, pagination, rate limits, compatibility, observability identifiers, and resource limits. Protocol syntax alone is insufficient. Clients need to know which failures are retryable and whether a timed-out operation may already have succeeded.

4. **[Deep dive] Why are timeouts, retries, and idempotency inseparable?**

   **Answer.** Without a timeout, a client can wait forever. After a timeout, it often cannot know whether the server performed the operation, so retry may duplicate effects. Idempotent operations or idempotency keys let the server return the original result for a repeated logical request. Retries need bounded attempts, exponential backoff, jitter, and a total deadline to avoid retry storms.

5. **[Design] How should a client-server system handle overload?**

   **Answer.** Bound queues and concurrency, reject excess work early with an explicit retryable response, propagate deadlines/cancellation, apply rate limits and admission control, and shed optional work. Autoscaling helps only within resource and startup limits. Unbounded queues convert overload into high latency and memory exhaustion while making the system appear temporarily available.

6. **[Deep dive] Which security boundaries should be assumed?**

   **Answer.** Treat all network input as untrusted: authenticate the caller, authorize each operation/resource, validate sizes and schemas, encrypt transport where needed, limit resource consumption, avoid leaking sensitive errors, and record auditable identities. Internal networks are not inherently trusted. Protect credentials and define replay resistance for sensitive operations.

   ```mermaid
   flowchart LR
       C[Client] -->|request + identity + deadline| G[Gateway / server boundary]
       G --> A[authentication and authorization]
       A --> V[validation and rate limit]
       V --> S[application service]
       S --> D[(durable state)]
   ```

---

# 14. Producer-consumer

1. **[Basic] What problem does the producer-consumer pattern solve?**

   **Answer.** Producers create work items and place them into a shared channel; consumers remove and process them, decoupling production rate and execution. The queue defines synchronization, buffering, ownership transfer, ordering, and shutdown behavior. It can be in-process or backed by a distributed broker.

2. **[Deep dive] Why is a bounded queue usually safer than an unbounded queue?**

   **Answer.** A bounded queue caps memory and makes overload visible. When full, the design must apply backpressure: block, reject, drop according to policy, sample, or spill to durable storage. An unbounded queue merely postpones overload, increasing latency until the process exhausts memory.

3. **[Code] Which condition-variable rules matter for a blocking queue?**

   **Answer.** Protect the queue and closed flag with one mutex; wait with a predicate in a loop because wakeups can be spurious and another consumer may win the item. Modify state while holding the lock, then notify. Move the item out under the lock but process it after unlocking. Define whether `push` after close fails and whether consumers drain remaining items.

   ```cpp
   std::optional<T> pop() {
       std::unique_lock lock(mutex_);
       ready_.wait(lock, [&] { return closed_ || !queue_.empty(); });
       if (queue_.empty()) return std::nullopt; // closed and drained
       T value = std::move(queue_.front());
       queue_.pop();
       space_.notify_one();
       return value;
   }
   ```

4. **[Deep dive] How should shutdown and cancellation be designed?**

   **Answer.** Closing the channel must wake blocked producers/consumers. Specify graceful drain versus immediate cancellation, who owns unprocessed items, and how failures are reported. In C++20, `std::stop_token` can cooperate with interruptible work, but waiting primitives and the queue protocol still need a clear closed state. Threads must be joined or otherwise owned safely.

5. **[Deep dive] What changes with multiple producers and consumers?**

   **Answer.** Global processing order may differ from enqueue order even if the queue is FIFO, because consumers execute concurrently. Per-key ordering may require partitioning or keyed serialization. Exactly-once processing is rarely obtained from a queue alone; use idempotent handlers, acknowledgements, durable state, and deduplication. Lock-free queues may reduce contention in specific workloads but make memory reclamation and correctness substantially harder.

---

# 15. MVC

1. **[Basic] What are the responsibilities of Model, View, and Controller?**

   **Answer.** The Model represents domain state, rules, and operations independently of presentation. The View renders state and collects presentation-level interaction. The Controller interprets input, invokes model/application operations, and selects or updates the view. Exact responsibilities vary across desktop, web, and framework variants; the essential goal is separating domain decisions from presentation concerns.

   ```mermaid
   flowchart LR
       U[User] --> V[View]
       V --> C[Controller]
       C --> M[Model]
       M -->|state / change notification| V
       V --> U
   ```

2. **[Deep dive] Should the Model know about the View?**

   **Answer.** Domain code should not depend on concrete UI classes. A framework may let views observe model changes through an abstract notification mechanism, but the dependency should not pull presentation types into the domain. Often an application/presentation model maps domain state into view data, which keeps formatting, localization, and UI scheduling outside the domain.

3. **[Deep dive] How do MVC, MVP, and MVVM differ?**

   **Answer.** MVC commonly lets a controller handle input while a view reads/observes the model. MVP places presentation logic in a Presenter that drives a mostly passive View interface. MVVM exposes bindable ViewModel state and commands to a View through data binding. Framework conventions matter more than labels; evaluate dependency direction, testability, and where state transformation lives.

4. **[Design] What are common MVC failure modes?**

   **Answer.** “Massive controllers” accumulate business logic, models become database records with no behavior, views perform domain decisions, and bidirectional updates create feedback loops. Keep use-case orchestration in application services, domain rules in the model, and display formatting in presentation code. Test domain and controller/presenter behavior without a real GUI, and test view wiring separately.

---

# 16. Publish-subscribe and event-driven systems

1. **[Basic] What is publish-subscribe?**

   **Answer.** Publishers emit messages to topics or a broker without naming individual consumers; subscribers express interest and receive matching messages. The broker can decouple participants in space, time, and synchronization, depending on whether messages are durable and delivery is asynchronous. This supports independent evolution but introduces distributed-state and observability challenges.

2. **[Deep dive] How does pub-sub differ from the in-process Observer pattern?**

   **Answer.** Observer is commonly synchronous, in-process, and tied to object lifetimes; failure may propagate directly to the caller. Pub-sub commonly crosses process boundaries through serialization and a broker, supports durable asynchronous delivery, and has retries, duplicates, lag, and independent deployment. An in-process event bus lies between them and can inherit hidden-dependency problems from both.

3. **[Deep dive] What do at-most-once, at-least-once, and exactly-once delivery mean?**

   **Answer.** At-most-once may lose a message but does not intentionally redeliver it. At-least-once retries until acknowledged and can deliver duplicates, so consumers must be idempotent. End-to-end exactly-once effects require the broker, consumer state, and external side effects to participate in one protocol; many “exactly once” claims cover only a limited boundary. Design for deduplication and replay explicitly.

4. **[Deep dive] What ordering can subscribers rely on?**

   **Answer.** Global total order is expensive and often unnecessary. Brokers commonly guarantee order only within a partition, key, or producer session; retries and parallel consumers can still affect observed processing order. Choose a partition key matching the invariant—for example account ID—and include sequence/version data when consumers must detect gaps or stale events.

5. **[Design] How should event schemas evolve?**

   **Answer.** Treat events as public, durable contracts: prefer additive optional fields, stable semantic names, tolerant readers, explicit defaults, and schema validation/compatibility checks. Do not repurpose a field with new meaning. For incompatible change, publish a new event type/version and run migration or dual-consumption deliberately. Retention means very old producers/messages may coexist with new consumers.

6. **[Deep dive] How do domain events, integration events, notifications, and event sourcing differ?**

   **Answer.** A domain event records something meaningful within a domain model. An integration event is a stable boundary message for other services. A notification may merely signal that receivers should query current state. Event sourcing uses an ordered event log as the authoritative state history, reconstructing state by replay; publishing events does not by itself make a system event-sourced.

7. **[Design] What operational concerns are essential in event-driven systems?**

   **Answer.** Monitor queue depth, consumer lag, processing latency, retry counts, dead-letter traffic, poison messages, and schema failures. Bound concurrency, propagate correlation/causation IDs, make handlers idempotent, define replay procedures, and protect downstream services with backpressure. Asynchronous decoupling moves failures in time; it does not remove them.

---

# 17. Naming and API consistency

1. **[Basic] What makes a good API name?**

   **Answer.** It expresses domain meaning and observable behavior rather than implementation technique. Use nouns for values/types, verbs for actions, and established project vocabulary. Avoid vague names such as `process`, `manager`, `data`, or `handle` unless the surrounding domain makes them precise. A longer specific name is often cheaper than repeated documentation and misuse.

2. **[Deep dive] What dimensions of consistency matter across an API?**

   **Answer.** Similar operations should use consistent naming, parameter order, units, ownership, nullability, error model, constness, synchronization, and return conventions. Paired operations should be predictable (`begin/end`, `lock/unlock`, `subscribe/unsubscribe`). Consistency reduces the amount clients must memorize, but do not preserve a misleading convention when semantics genuinely differ—make the difference visible.

3. **[Code] How can C++ types communicate ownership, units, and optionality better than names alone?**

   **Answer.** Use values for independent results, `unique_ptr` for ownership transfer, references for required borrowing, pointers/`optional` for absence according to project convention, `span`/`string_view` for bounded views, and strong domain types for units/IDs. Prefer `Timeout` or `std::chrono::milliseconds` to an `int timeout`, and `UserId` to interchangeable integer IDs. Types make invalid calls fail at compile time.

4. **[Deep dive] Which C++ qualifiers and attributes are part of API design?**

   **Answer.** `const` communicates observable mutation, ref-qualifiers distinguish lvalue/rvalue use, `noexcept` is a failure/performance contract, `explicit` prevents accidental conversions, `[[nodiscard]]` highlights results that should be checked, and concepts document template requirements. Apply them truthfully and consistently; an incorrect `noexcept` or misleading `const` is worse than omission.

5. **[Design] How should an API be reviewed for usability before implementation is fixed?**

   **Answer.** Write realistic call-site examples, including success, failure, cancellation, and lifetime boundaries. Check whether safe use is the shortest path, whether resource ownership is visible, and whether two similar functions behave similarly. Prototype with a small fake implementation and tests; call-site-first design often exposes ambiguous booleans, wrong parameter order, and missing domain types early.

6. **[Deep dive] Why are boolean parameters and sentinel values often problematic?**

   **Answer.** Calls such as `open(path, true, false)` hide meaning and allow invalid combinations. Replace them with enums, option structs, or distinct operations. Sentinel values such as `-1` or empty string mix data with control state and can collide with valid future values; use `optional`, a result type, or a dedicated variant.

---

# 18. Error handling

1. **[Basic] Compare exceptions, error codes, and `std::expected`.**

   **Answer.** They represent different control-flow and API tradeoffs:

   | Mechanism | Strengths | Costs / risks | Good fit |
   | --- | --- | --- | --- |
   | Exceptions | Separate happy path; propagate automatically; work with constructors | Hidden control flow; require exception-safe code; may be disabled at some boundaries | Failures that cannot be handled locally, rich C++ application code |
   | Error codes | Explicit and ABI/C friendly; no unwinding | Easy to ignore; output parameters; manual propagation; weak context | C APIs, kernels/embedded constraints, very small error domains |
   | `std::expected<T, E>` | Explicit typed error and value; composes without exceptions | Verbose propagation; error type affects API/templates; C++23 | Expected recoverable failures at subsystem boundaries |

   A codebase can use more than one mechanism at carefully defined boundaries.

2. **[Deep dive] When should an error not be represented as a recoverable return value?**

   **Answer.** Programmer contract violations—out-of-range under a documented precondition, broken invariants, impossible states—are usually assertions, contract failures, or fatal errors rather than routine branches callers should handle. Resource exhaustion and environmental failures may be recoverable depending on the system. Distinguish bugs from expected domain outcomes such as “user not found” or validation failure.

3. **[Basic] When are exceptions a good design choice?**

   **Answer.** They work well when failure is exceptional relative to the caller, intermediate frames cannot usefully handle it, constructors must report failure, and RAII makes unwinding safe. Catch at a layer that can recover, translate, retry, or present the error—not at every function. Do not use exceptions as ordinary loop/control-flow signals in performance-sensitive paths without measurement.

4. **[Basic] When are error codes or `expected` preferable?**

   **Answer.** They are useful when failure is frequent and expected, callers must visibly branch, the environment forbids exceptions, or the boundary is C/ABI-sensitive. `expected<T, E>` couples a value with a typed error and prevents accidentally reading a missing value; choose a stable error type with machine-readable categories and optional context. Plain integer codes need disciplined checking and a separate way to return values/context.

5. **[Code] What does a useful `std::expected` API look like?**

   **Answer.** Return the successful value directly and define a domain-level error rather than exposing raw transport/library errors. Callers can branch explicitly and, with C++23 operations, transform or chain results.

   ```cpp
   enum class LoadErrorCode { not_found, permission_denied, invalid_format };

   struct LoadError {
       LoadErrorCode code;
       std::string context;
   };

   std::expected<Config, LoadError> load_config(const Path& path);
   ```

   Avoid making the error object so unstable or large that every public signature becomes hard to evolve.

6. **[Deep dive] How should errors cross subsystem or language boundaries?**

   **Answer.** Translate low-level errors into the receiving layer's vocabulary while preserving diagnostic cause/context. Never let C++ exceptions escape through a C ABI, plugin ABI with incompatible runtimes, thread entry point, or callback that forbids them. Boundary code catches all expected exceptions, maps them to a stable status/result, and prevents sensitive details from leaking.

7. **[Deep dive] Where should errors be logged?**

   **Answer.** Usually log once at the boundary that decides the failure's final disposition. Low-level code should return/throw structured context rather than log and propagate, which produces duplicates and false alarms when a caller retries or recovers. Add correlation IDs and causal context as the error moves upward; do not include secrets. Metrics may be recorded separately from human-readable logs.

8. **[Deep dive] How do `noexcept`, destructors, and exception guarantees affect the error strategy?**

   **Answer.** Destructors and rollback operations should not emit exceptions; expose a fallible `close`/`commit` operation if callers must observe failure. Mark `noexcept` only when violation should terminate. For every mutating operation, decide whether failure leaves no change (strong guarantee), a valid changed state (basic), or cannot occur; the chosen error channel does not replace state-safety design.

---

# 19. Compatibility, ABI stability, and library distribution

1. **[Basic] Distinguish source, binary, and semantic compatibility.**

   **Answer.** Source compatibility means existing client source still compiles against the new version. Binary/ABI compatibility means already compiled clients can link and run without rebuilding. Semantic compatibility means behavior and documented contracts remain acceptable. A change can preserve source syntax while breaking ABI or behavior—for example reordering private fields in an exported class.

2. **[Deep dive] Which common C++ changes can break ABI?**

   **Answer.** Changing class size/layout, member/base order, virtual function order, inheritance, calling convention, parameter/return types, exception specification where encoded, inline definitions, template instantiations, RTTI settings, compiler standard library, packing, or symbol visibility can break binary clients. Adding a virtual function or a private data member is not ABI-neutral for a class passed across the boundary. Exact rules depend on the platform ABI and toolchain.

3. **[Deep dive] How does PImpl help ABI stability, and what can it not solve?**

   **Answer.** A fixed-size pointer in the public object hides private layout, allowing `Impl` fields and dependencies to change without changing `sizeof(PublicType)`. It also reduces header rebuilds. Public function signatures, vtables, allocator/runtime ownership, exception behavior, and semantics can still break compatibility. PImpl must define destruction in a context where `Impl` is complete and must deliberately implement copy/move behavior.

4. **[Basic] What are the advantages and disadvantages of a header-only library?**

   **Answer.** Header-only distribution is simple, exposes templates for arbitrary types, enables inlining/constant evaluation, and avoids a separate binary ABI. It increases client compile times, repeats instantiation/code, exposes implementation and dependencies, makes implementation changes rebuild clients, and risks ODR/configuration mismatches. It does not eliminate compatibility concerns; clients must recompile and may combine objects built with different header configurations.

5. **[Basic] What are the advantages and disadvantages of a compiled library?**

   **Answer.** A compiled library hides implementation, reduces client compilation, centralizes code generation, can patch implementation without recompiling clients when ABI is stable, and controls symbol visibility. It requires platform/configuration-specific artifacts and disciplined ABI/toolchain compatibility. Cross-boundary memory ownership, exceptions, runtime libraries, and STL types require particular care.

6. **[Design] When is a hybrid library design appropriate?**

   **Answer.** Put templates, small stable value types, and customization points in headers; place non-template algorithms and volatile dependencies behind compiled functions or PImpl. Explicitly instantiate popular template combinations in the library when that meaningfully reduces build cost. This balances optimization and genericity against encapsulation and ABI stability.

7. **[Deep dive] How should a public API evolve while preserving backward compatibility?**

   **Answer.** Prefer additive changes, overloads with unambiguous defaults, new versioned entry points, and deprecation windows. Do not silently change semantics or reuse enum/numeric values. Keep compatibility tests that compile and, for ABI commitments, run old clients against new binaries; use ABI-diff tooling and symbol/version policies. Remove deprecated features only at a declared major-version boundary.

8. **[Design] What should a stable plugin boundary expose?**

   **Answer.** Prefer a small C ABI with versioned structs/function tables, opaque handles, explicit create/destroy functions, fixed-width types, negotiated capability/version fields, and caller-provided buffers or clearly paired allocators. Do not pass STL containers, C++ exceptions, compiler-specific class layouts, or ownership across unknown runtimes. Validate plugin versions and keep each side responsible for freeing what it allocated.

---

# Assessment usage notes

- For a short screening, select 8–12 **[Basic]** questions and 2–3 **[Code]** or **[Design]** questions across principles, patterns, and architecture.
- For a 60–90 minute interview, choose one concrete system or codebase and revisit the principles through that scenario instead of asking only for definitions.
- Strong answers identify tradeoffs and failure modes. Naming a pattern without explaining why it fits is not sufficient.
- Ask candidates to state ownership, lifetime, concurrency, error, and compatibility contracts explicitly; those details distinguish implementable designs from diagrams.
- Accept simpler designs when they satisfy the current forces. Pattern count is not a measure of design quality.
