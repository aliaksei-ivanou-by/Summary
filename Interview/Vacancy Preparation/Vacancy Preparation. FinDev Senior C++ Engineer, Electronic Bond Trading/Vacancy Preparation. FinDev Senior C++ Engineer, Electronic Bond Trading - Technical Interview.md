# FinDev Senior C++ Engineer - Vacancy-Specific Technical Questions

> Vacancy-specific question bank for the FinDev Senior C++ Engineer role. General knowledge lives in the shared banks; this file keeps only cross-stack, product-specific and architecture questions. `ROLE-` identifiers are local to this vacancy folder.

> Russian version of this file: [Technical Interview - RU](<./Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading - Technical Interview - RU.md>). Both files carry the same content and must not drift apart.

> Main vacancy preparation: [FinDev Senior C++ Engineer](<./Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading.md>).

# Shared Question Banks

| Bank | Coverage |
|---|---|
| [C++ Core Questions](<../Technical Interview/C++ Core Questions.md>) | Language, build/link model, STL, ownership, concurrency, algorithms, systems, performance and design |
| [C Language Questions](<../Technical Interview/C Language Questions.md>) | C89 and later standards, undefined behaviour, memory sections, alignment and packing, strings, function pointers, opaque structs, static and shared libraries, core dumps and sanitizers |
| [Databases and SQL Questions](<../Technical Interview/Databases and SQL Questions.md>) | Joins, indexes and query plans, N+1, ACID and isolation, deadlocks, SQLite vs a server database |
| [Linux and Shell Questions](<../Technical Interview/Linux and Shell Questions.md>) | Permissions, processes and signals, hung-process diagnosis, systemd, redirection, shell scripting, production diagnosis |
| [Build Systems Questions](<../Technical Interview/Build Systems Questions.md>) | Make, target-based CMake, dependencies, cross-compilation, build speed |
| [Testing Questions](<../Technical Interview/Testing Questions.md>) | Testing theory, GoogleTest fixtures and parameterized tests, GoogleMock, testing threads and time, legacy code, CI layering |
| [Containers and Orchestration Questions](<../Technical Interview/Containers and Orchestration Questions.md>) | Docker images and layers, multi-stage builds for C++, debugging in a container, Kubernetes objects and probes |
| [Git Questions](<../Technical Interview/Git Questions.md>) | Commit graph, merge vs rebase, conflicts, reset vs revert, branching strategy, bisect |
| [JavaScript and Node.js Questions](<../Technical Interview/JavaScript and Node.js Questions.md>) | Not required by this vacancy; kept linked because the banks are shared |
| [COM and Excel Questions](<../Technical Interview/COM and Excel Questions.md>) | Not required by this vacancy; kept linked because the banks are shared |
| [Live Coding Scaffold](<../Technical Interview/Live Coding Scaffold.md>) | CMake/GoogleTest scaffold, the clarifying questions to ask first, and two worked exercises |
| [Behavioral Questions](<../Behavioral Questions.md>) | Decision defended, being wrong, disagreement, hardest bug, time zones, mentoring, weakness, deadlines, why you, salary |

# Question Index

Questions use stable topic-specific IDs. Every answer begins with a short bullet summary and keeps details, examples and edge cases below it.

## 1. Schema-Driven Interfaces (ROLE-001–ROLE-003)

| | | |
|---|---|---|
| [ROLE-001. Protobuf or FlatBuffers - how would you choose?](#question-role-001) | [ROLE-002. How do you evolve a schema without breaking a deployed service?](#question-role-002) | [ROLE-003. What does a shared service framework with code generation buy, and what does it cost?](#question-role-003) |

## 2. Domain Model and State (ROLE-004–ROLE-007)

| | | |
|---|---|---|
| [ROLE-004. How would you model the lifecycle of a trade negotiation?](#question-role-004) | [ROLE-005. Two services disagree about whether a line is still live. How do you find out which is right?](#question-role-005) | [ROLE-006. What makes a trading message idempotent, and how do you implement it?](#question-role-006) |
| [ROLE-007. A list of 500 bonds, each line with its own negotiation state. How do you handle it?](#question-role-007) | | |

## 3. Production Operation and Delivery (ROLE-008–ROLE-012)

| | | |
|---|---|---|
| [ROLE-008. A request that took 2 ms now takes 40 ms in production. How do you find out why?](#question-role-008) | [ROLE-009. A C++ service leaks in production but not in staging. What do you do?](#question-role-009) | [ROLE-010. How would you design the integration service bridging to an external trading system?](#question-role-010) |
| [ROLE-011. How do you test a service whose behaviour depends on other services?](#question-role-011) | [ROLE-012. How do you deploy a change to a service that is holding live trading state?](#question-role-012) | |

---

# 1. Schema-Driven Interfaces

## Question ROLE-001

[↑ Back to question index](#question-index)

### Question ROLE-001 — Protobuf or FlatBuffers - how would you choose?

**Short answer**

- Protobuf encodes to a compact wire format that must be parsed into objects before use; FlatBuffers lays the data out so that fields are read in place through offsets, with no parse step and no allocation.
- So the decision is about the access pattern: Protobuf when the message is fully consumed, must be small on the wire, or crosses many languages and tools; FlatBuffers when a large message is read partially or repeatedly on a latency-sensitive path.
- The cost of FlatBuffers is rigidity - append-only table evolution, `struct`s whose layout can never change, awkward mutation - and larger messages; the cost of Protobuf is the parse and the allocations it implies.

**Details and nuances**

**Protobuf** encodes each field as a tag-length-value record with varint integers, so absent fields cost nothing and small numbers are small. Deserialization walks the buffer and builds a message object, which means allocation and copying proportional to the message. That is a real cost on a hot path and completely irrelevant on a control path.

**FlatBuffers** writes the data in its final in-memory layout. Each table carries a vtable of field offsets, so reading field *n* is an offset lookup and a load - you can open a one-megabyte message, read two fields, and never touch the rest. Absent fields return the schema default at no storage cost. The buffer is the object, so there is no unpack step, no per-message allocation and nothing to free.

| | Protobuf | FlatBuffers |
|---|---|---|
| Access | parse into objects first | read in place through offsets |
| Cost model | proportional to message size | proportional to fields touched |
| Wire size | smaller (varints, no padding) | larger (alignment and vtables) |
| Mutation | natural | scalars in place; otherwise unpack to the object API |
| Evolution | field numbers, `reserved`, rich compatibility rules | append-only fields, `deprecated`; `struct`s frozen |
| Ecosystem | very broad - gRPC, tooling, every language | narrower |

**A mixed codebase is normal and not a contradiction.** Persisted records and control-plane RPC in Protobuf, because they are fully consumed and want the tooling; high-rate messages where a consumer reads a few fields of a large payload in FlatBuffers. That both are named in the posting is more likely to reflect that split than an unfinished migration - but it is worth asking which, because the answer says a lot about where the performance-sensitive boundary in the system actually is.

**The trap to avoid in the answer** is presenting zero-copy as free. FlatBuffers also costs you: the schema is harder to change safely, the generated API is less pleasant, a `struct` is a permanent commitment because its layout is fixed, and if the consumer reads the whole message anyway you have paid in size and convenience for an advantage you never use. The right framing is "which access pattern does this interface actually have", not "which library is faster".

**Evidence boundary:** this is prepared knowledge and one evening's exercise, not production experience with either library. Say so. The transferable part is the reasoning about access patterns and about formats designed to change, which comes from real work on custom inter-service protocols and a hand-rolled persisted format.

**Likely follow-ups**

- "What about Cap'n Proto, SBE or just a packed struct?" - Cap'n Proto is the other zero-copy option; SBE is the fixed-layout format built for market data specifically; a packed struct is fastest and has no evolution story at all, which is why nobody ships it across teams ([C-009](<../Technical Interview/C Language Questions.md#question-c-009>)).
- "Where does JSON fit?" - at the edges, for human-facing and debugging interfaces, never on the hot path.

[↑ Back to question index](#question-index)

---

## Question ROLE-002

[↑ Back to question index](#question-index)

### Question ROLE-002 — How do you evolve a schema without breaking a deployed service?

**Short answer**

- The field number is the contract, not the field name: adding a new optional field is safe, removing one means reserving its number forever, and reusing a retired number is the change that corrupts data silently.
- Every change has to be safe in both directions at once, because in a rolling deploy an old binary and a new binary are running simultaneously and reading each other's messages.
- Anything that cannot be expressed as an additive change - a semantic change to an existing field, a required-to-optional transition, splitting one field into two - is a two-phase migration: write both, switch readers, then retire the old field.

**Details and nuances**

**Protobuf's rules, precisely.** Adding a field with a new number is backward and forward compatible: an old reader ignores it, and in proto3 unknown fields are preserved rather than dropped, so a message that passes through an old intermediate service comes out intact. Removing a field means deleting the code *and* writing `reserved 7;` and `reserved "old_name";` so no one can ever reuse either. Renaming a field is safe on the wire and breaks anything using the text or JSON representation.

Type changes are where the detail matters. `int32`, `int64`, `uint32`, `uint64` and `bool` are wire-compatible with each other, with the caveat that a negative number written as `int32` and read as `int64` sign-extends to the same value but occupies ten bytes; `sint32` and `sint64` are compatible with each other but *not* with the plain variants because of zigzag encoding; `fixed32`/`sfixed32` and `fixed64`/`sfixed64` pair up; `string` and `bytes` are compatible when the content is valid UTF-8. Changing between anything else is a silent reinterpretation. Moving a single existing field into a new `oneof` is safe; moving several into one is not, because only one can then be set.

**FlatBuffers is stricter and that is the point.** New fields may only be *appended* to a table, because the vtable maps field order to slots - unless you pin identity explicitly with `id:` attributes, in which case you can reorder the declarations but still not reuse an id. Fields are never removed, only marked `deprecated`, which keeps the slot occupied and removes the accessor. `struct`s cannot be evolved at all: their layout is fixed by design, which is exactly why they are fast, so anything that might ever gain a field must be a table.

**The deployment constraint is the real answer.** In a rolling deploy the two versions coexist, so "backward compatible" is not enough - a new writer's message must be readable by an old reader, and an old writer's message by a new reader, at the same time. That rules out any change whose correctness depends on both sides being upgraded together. The general shape of a non-additive change is therefore:

1. Add the new field. Writers populate both old and new; readers still use the old.
2. Deploy everywhere. Now every instance can produce and understand both.
3. Switch readers to the new field. Deploy.
4. Stop writing the old field. Deploy.
5. Reserve its number. It is now permanently retired.

Four deploys to rename a field is not bureaucracy; it is the price of never having a window in which a message means two different things.

**The discipline around it** is what makes this survivable at scale: schemas in version control with review, generated code never edited by hand, and a compatibility check in CI - `buf breaking` or the equivalent - so a breaking change fails the build rather than the trading day. Semantic changes are the class no tool catches: keeping the field and changing what the value means passes every compatibility checker and breaks every consumer. The safe move there is always a new field.

**Evidence boundary:** the tooling is prepared knowledge. The experience behind the reasoning is a persisted format I deliberately designed to be evolved by explicit mapping rather than generic serialization, precisely so that adding a field was a reviewable diff - the hand-rolled version of what these tools provide properly ([backup story in the main file](<./Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading.md>)).

**Likely follow-ups**

- "How would you detect that someone reused a field number?" - a compatibility check in CI against the previous released schema; `reserved` makes the compiler catch it, which is why reserving is not optional.
- "What if the message is persisted, not just transmitted?" - then the old version never goes away and every reader must handle every historical version; retention policy becomes part of the schema decision.
- "How does this interact with the database?" - the same two-phase shape: add a nullable column, backfill, switch readers, drop later ([DB-001](<../Technical Interview/Databases and SQL Questions.md#question-db-001>)).

[↑ Back to question index](#question-index)

---

## Question ROLE-003

[↑ Back to question index](#question-index)

### Question ROLE-003 — What does a shared service framework with code generation buy, and what does it cost?

**Short answer**

- It buys uniformity: every service gets the same wiring, transport, lifecycle, logging, metrics and message types, so a new service is domain code plus a schema rather than a new set of decisions.
- It costs coupling and opacity: the framework version becomes a shared dependency across every service, generated code is template-heavy and hard to read through in a debugger, and anything the framework did not anticipate is expensive to do.
- The engineering skill it actually demands is reading generated and template-heavy code fluently, and knowing the difference between a problem in your code and a problem in the layer underneath.

**Details and nuances**

**What is genuinely gained.** One way to define an interface, one way to start and stop a service, one health and metrics surface, one place where a cross-cutting change - a new tracing header, a retry policy, a logging field - is made once rather than in thirty repositories. In a distributed platform that consistency is worth more than any individual abstraction in it, because it makes services comparable when something goes wrong at three in the morning.

**What is genuinely lost.** The framework version is a coupling point: upgrading it touches everything, so upgrades get deferred, and deferring them makes the next one worse. Generated code is machine-shaped - long, template-heavy, full of names you did not choose - and a stack trace through it is significantly harder to read than one through your own code. And the moment a requirement falls outside what the framework models, you are choosing between a fork, a workaround and a patch upstream, none of which is cheap.

**Why the posting stresses template-heavy code twice.** This is the answer to that. A generated-plus-framework codebase is where you meet heavy template use not as a design choice but as a fact of the environment: you will read error messages that are pages long, step into instantiations in a debugger, and reason about which overload the compiler actually chose. The skills that matter are concrete - `-fconcepts-diagnostics-depth` or the equivalent for reading diagnostics, knowing that concepts produce better messages than `enable_if` for exactly this reason ([CPP-113](<../Technical Interview/C++ Core Questions.md#question-cpp-113>)), reading the preprocessed or generated output when the source is not enough, and understanding instantiation well enough to know why a change in one header changed the build everywhere ([CPP-111](<../Technical Interview/C++ Core Questions.md#question-cpp-111>), [CPP-093](<../Technical Interview/C++ Core Questions.md#question-cpp-093>)).

**Build time becomes a real cost.** Templates and generated headers instantiate in every translation unit that includes them, so the framework is usually the dominant term in compile time. The levers are the usual ones and they are worth naming: forward declarations and PImpl at boundaries ([CPP-143](<../Technical Interview/C++ Core Questions.md#question-cpp-143>)), explicit instantiation of common specializations in one translation unit, precompiled headers, `ccache`, and unity builds as a blunt instrument ([BLD-010](<../Technical Interview/Build Systems Questions.md#question-bld-010>)).

**The position to take in the interview** is neither enthusiasm nor scepticism but the tradeoff: a shared framework is the right call when you have many similar services and the cost of divergence exceeds the cost of coupling, which is exactly the situation the posting describes. The question I would ask back - and it is in the employer questions list - is where it gets in the way, because every team that has one has an answer and it tells you what the work actually feels like.

**Evidence boundary:** I have not worked on a generated-code service framework. The adjacent experience is a platform with custom inter-service protocols where the payload definitions were shared but not generated - the same coupling problem without the tooling that makes it manageable.

[↑ Back to question index](#question-index)

---

# 2. Domain Model and State

## Question ROLE-004

[↑ Back to question index](#question-index)

### Question ROLE-004 — How would you model the lifecycle of a trade negotiation?

**Short answer**

- As an explicit state machine per line item, with the transitions enumerated, every transition triggered by a named event, and no way to reach a state except through a transition.
- Amendments, cancellations, timeouts and partial execution are states and transitions, not error handling bolted onto the side - they are the normal life of a negotiation, not exceptions to it.
- What varies by protocol should be data or policy attached to the machine, not a parallel machine per protocol, because "a growing set of trading protocols" is the requirement that a copy-per-protocol design fails.

**Details and nuances**

**Make the states enumerable and the transitions total.** A negotiation line moves through something like: created, sent for quote, quoted, in negotiation, accepted, executed, partially executed, cancelled, expired, rejected, failed. The value of writing them down is not documentation - it is that every event in every state then has a defined answer, including "ignore" and "reject", so there is no implicit behaviour to discover later. An `enum class` plus a transition function that takes the current state and an event and returns the next state makes the whole model reviewable in one place, and makes an illegal transition a compile-time or immediately-testable concern rather than a production surprise.

**Terminal states must be genuinely terminal.** The commonest bug class in this kind of model is a late message arriving after a line has been cancelled or filled and quietly reviving it. Every transition out of a terminal state is rejected and logged, not silently applied - and the log line is what tells you the counterparty's view and yours have diverged.

**Time is an input, not a background condition.** An expiry is an event delivered to the machine, produced by a timer that is itself part of the state, so that it is testable. Wall-clock reads scattered through the logic make the behaviour untestable and non-deterministic; an injected clock makes the same code exercisable in milliseconds ([TEST-013](<../Technical Interview/Testing Questions.md#question-test-013>)).

**Protocol variation is the design question.** The instinct is a subclass or a separate machine per protocol; that scales linearly in code and quadratically in bugs, because a fix to shared behaviour now needs applying *n* times. The alternative is one machine plus a per-protocol policy object supplying the parts that genuinely differ - which events exist, which transitions are permitted, timeout durations, whether counterparties are disclosed, whether partial execution is allowed. Adding a protocol is then a new policy and a test suite, not a new model. Where a protocol is structurally different enough that the policy becomes a pile of conditionals, that is the signal it deserves its own machine after all, and noticing that boundary is the actual judgement being tested.

**Persistence follows from the model.** Either the current state is persisted and transitions update it, or the events are persisted and the state is derived by replay. Event-sourcing gives you a full audit trail - which a regulated trading workflow is likely to want anyway - and makes recovery after a restart a replay rather than a reconstruction; it costs storage, replay time and schema-evolution discipline on the event types ([ROLE-002](#question-role-002)). Persisting the state alone is simpler and loses the history. Which one the platform uses is worth asking, because it determines what recovery looks like ([ROLE-012](#question-role-012)).

**Evidence boundary:** the domain is new to me. The state-machine reasoning is not: on the SIP platform I implemented a registration watchdog and a primary/backup failover/failback machine, and the lesson I took from it - put the machine where the events actually arrive, and let everything else observe rather than reconstruct - is the same lesson this question is about ([Story 2 in the main file](<./Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading.md>)).

[↑ Back to question index](#question-index)

---

## Question ROLE-005

[↑ Back to question index](#question-index)

### Question ROLE-005 — Two services disagree about whether a line is still live. How do you find out which is right?

**Short answer**

- First establish which service *owns* the state; the other one holds a cache, and a cache disagreeing with its source is a different bug from two owners disagreeing.
- Then reconstruct the sequence from both sides using a correlation identifier that follows the line item across every hop, and compare it against the transition log rather than against the current state.
- The durable fix is almost never better reconciliation - it is removing the second copy, or making the second copy explicitly derived with a version that lets a stale read be detected.

**Details and nuances**

**The ownership question comes first because it changes the whole investigation.** If service A owns the state and service B caches it, the question is why B's copy is stale: a missed message, an out-of-order delivery, a failed invalidation, a retry that re-applied an old value. If both services believe they own it, there is no fact of the matter to discover - the design is wrong and the incident is a symptom.

**Reconstructing the sequence needs three things in place before the incident**, which is why this question is really about instrumentation:

- A **correlation identifier** on the line item that is propagated through every message and appears in every log line, so one grep assembles the whole path.
- A **transition log**, not just current state: what event arrived, in what state it found the line, what it transitioned to, and when. Current state tells you the services disagree; the transition log tells you where they diverged.
- **Monotonic versioning** on the entity - a sequence number or version incremented on every transition. Then a stale read is detectable rather than plausible, and a message carrying an old version can be rejected instead of applied.

Without a version, out-of-order delivery is invisible: the messages look fine individually, and the wrong one simply arrives last. With a version, the same situation is a logged rejection.

**The common causes, roughly in order of frequency:** a retry delivered twice with no deduplication so a transition applied out of order ([ROLE-006](#question-role-006)); ordering assumed but not guaranteed, because the transport only preserves order per partition or per connection; a timeout on the caller that the callee completed anyway, so one side thinks it failed and the other thinks it succeeded; and two copies of state maintained independently, which is not really a race but a design choice that had to fail eventually.

**The fix is structural.** Exactly one component owns a piece of state; everything else observes it, or holds a copy explicitly labelled as derived, with a version that makes staleness detectable. I would rather delete the second copy than build a reconciler, and if a reconciler is genuinely needed it should be a scheduled, alerting, auditable job - not an ad-hoc repair path someone runs during an incident.

**Evidence boundary:** this is directly transferable from work I have done. On the library platform the recurring shape was exactly this - a symptom in the Java client or the API whose cause was in C, in a protocol payload or in runtime state - and the method was correlating logs across the request path before touching code ([Story 1](<./Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading.md>)). On the SIP platform the fix for a comparable inconsistency was to move state ownership into the component that could see the transitions in order, and have the rest observe it.

[↑ Back to question index](#question-index)

---

## Question ROLE-006

[↑ Back to question index](#question-index)

### Question ROLE-006 — What makes a trading message idempotent, and how do you implement it?

**Short answer**

- Idempotent means processing the same message twice has the same effect as processing it once - which requires a stable identifier chosen by the sender and remembered by the receiver, not a property of the transport.
- Almost every real transport gives at-least-once delivery, so duplicates are normal traffic and the receiver, not the network, is where exactly-once semantics are actually implemented.
- The two mechanisms are a deduplication store keyed by that identifier, and state transitions written so that re-applying them is a no-op - and the second is better wherever the domain allows it.

**Details and nuances**

**Why duplicates are guaranteed rather than unlikely.** A sender that times out cannot distinguish "the message was lost" from "the message arrived and the response was lost", so a correct sender retries and an incorrect one drops work. Add a broker that redelivers on unacknowledged messages, a reconnect that replays from a sequence number, and a failover that resumes from a checkpoint, and duplicates are part of the normal operating envelope. "Exactly once" as a transport property does not exist; what exists is at-least-once delivery plus an idempotent receiver.

**The identifier must come from the sender and be stable across retries.** A receiver-generated id is useless - the retry gets a different one. In FIX this is exactly what `ClOrdID` is for, with `OrigClOrdID` chaining a cancel or replace back to the order it modifies, and session-level `MsgSeqNum` giving gap detection and resend on top of it. A platform with its own protocols needs the same two ideas under whatever names it uses, and the interview-useful observation is that FIX solved this decades ago in precisely this way.

**Mechanism one: a deduplication store.** Record the identifier and the outcome; on a repeat, return the recorded outcome without re-executing. Three details decide whether it actually works: the record and the effect must be written atomically, or a crash between them leaves you having done the work without remembering it; the retention window must exceed the maximum retry horizon, or a late duplicate is treated as new; and the store must be as available as the service, which is why an in-memory map is not enough and Redis or the primary database is the usual home.

**Mechanism two: transitions that are naturally idempotent.** "Set state to cancelled" applied twice is the same as once; "decrement remaining quantity by 100" is not. Where the domain permits absolute rather than relative updates, and where a transition out of a terminal state is rejected rather than applied ([ROLE-004](#question-role-004)), duplicates become harmless without any bookkeeping. This is the better answer whenever it is available, because it removes an entire failure mode instead of managing it.

**Ordering is a separate problem and must not be conflated with it.** Deduplication stops a message being applied twice; it does nothing about two different messages arriving in the wrong order. That needs a sequence number per entity and a rule for what to do with a gap - buffer briefly, or reject and request a resend. A system that deduplicates but does not order will still corrupt state, quietly.

**And the honest caveat:** deduplication is not free. It is a write on every message and a lookup on every message, it introduces a dependency that must be available, and its retention window is a real operational parameter that someone has to choose and monitor.

**Evidence boundary:** prepared knowledge. The closest thing I have done is designing recovery around a SIP registration failover, where a re-registration after a switch had to leave the phone in a consistent state rather than compound the previous one - the same instinct, at a much smaller scale and without a deduplication store.

[↑ Back to question index](#question-index)

---

## Question ROLE-007

[↑ Back to question index](#question-index)

### Question ROLE-007 — A list of 500 bonds, each line with its own negotiation state. How do you handle it?

**Short answer**

- The list is an aggregate with its own identity and its own state, and each line is an independent state machine underneath it - so the first design decision is which operations are per line and which are atomic across the whole list.
- Treat it as a bulk problem rather than 500 individual ones: one persisted transaction per batch rather than per line, one outbound message carrying many lines, and status derived from the lines rather than maintained separately.
- The parts that actually get hard are partial outcomes, per-line versus list-level timeouts, and the fact that a client watching the list wants aggregate progress at human speed while the lines change far faster.

**Details and nuances**

**Establish the atomicity boundary first, because it is a business question, not a technical one.** In list trading each line is quoted individually, so lines progress independently and a partial result is a normal outcome. In portfolio trading the basket is priced as one package, so all-or-nothing is the point. A service supporting both has to represent both, and the answer that shows domain thinking is to ask which one the operation is before designing anything.

**Do everything in batches.** Five hundred lines is not a scale problem in itself - it is a scale problem if every line becomes its own database round trip, its own outbound message and its own log entry. One transaction covering the batch, one message carrying many lines, and a bulk insert rather than a loop of inserts ([DB-008](<../Technical Interview/Databases and SQL Questions.md#question-db-008>)). The same reasoning applies to the outbound side: a counterparty receiving 500 separate requests when it could have received one list will rate-limit you, and rightly.

**Derive list state; do not store it twice.** "How many lines are quoted" is a function of the lines. Maintaining a counter alongside them creates exactly the two-owners-of-one-fact problem that produces disagreement ([ROLE-005](#question-role-005)). If deriving it is too slow at query time, the cache is explicitly derived, versioned and rebuildable - not a second source of truth.

**Timeouts exist at both levels and they interact.** A line can expire while the list is still live; the list can expire with lines still in flight, which requires cancelling them and handling the cancels that fail because the line already executed. That last case is the one to mention unprompted, because it is where a naive design produces a trade nobody meant to do.

**Updates to the client are a rate-mismatch problem.** A trader watching a 500-line list does not need every line transition rendered; they need the aggregate and the lines that changed, at a rate a human can read. That is coalescing and throttling - keep the latest state per line in a dirty map and flush on a timer, so ten transitions on one line inside a refresh window become one update ([CPP-076](<../Technical Interview/C++ Core Questions.md#question-cpp-076>), [CPP-075](<../Technical Interview/C++ Core Questions.md#question-cpp-075>)). Memory is then bounded by the number of lines rather than the number of events, and the bound holds under a burst.

**Concurrency: parallelise across lines, serialise per line.** Lines are independent, so they can be processed concurrently; a single line must see its own events in order, or the state machine is meaningless. The clean implementation is one owner per line - sharding lines across workers by identity so that a given line is always handled by the same worker - rather than a lock per line, which is where the lock-ordering problems start ([CPP-057](<../Technical Interview/C++ Core Questions.md#question-cpp-057>)).

**Evidence boundary:** this is a design answer, not an experience claim. The batching, coalescing and rate-mismatch reasoning is prepared; the "one owner per piece of state" rule is the one I arrived at through the SIP failover work.

[↑ Back to question index](#question-index)

---

# 3. Production Operation and Delivery

## Question ROLE-008

[↑ Back to question index](#question-index)

### Question ROLE-008 — A request that took 2 ms now takes 40 ms in production. How do you find out why?

**Short answer**

- Establish what changed and when before profiling anything: a regression with a start time is a deploy, a config change, a data-volume threshold or a dependency, and knowing which eliminates most of the search space.
- Then decompose the 40 ms rather than sampling it - inbound deserialization, own processing, database and cache round trips, downstream calls, outbound serialization - because in a distributed service the time is usually *between* components, not inside one.
- Only when the time is located inside this process does a profiler become the right tool; before that, tracing and timing instrumentation answer the question faster.

**Details and nuances**

**Start with the shape of the change, not the code.** Is it a step or a ramp? A step points at a deploy, a configuration change, a feature flag, or a dependency that changed underneath you; a ramp points at data growth, a cache that has stopped fitting, a leak, or a table that has outgrown its index ([DB-006](<../Technical Interview/Databases and SQL Questions.md#question-db-006>)). Is it all requests or a subset - a particular protocol, a particular list size, a particular counterparty? Is it one instance or all of them? Each of those questions removes more candidates than an hour of profiling.

**Ask for the right number.** "40 ms" is a mean unless stated otherwise, and a mean can move because the median moved or because the tail did, which are different problems with different causes ([CPP-073](<../Technical Interview/C++ Core Questions.md#question-cpp-073>)). A tail that moved while the median did not is usually contention, garbage in the allocator, a retry path, or one slow dependency.

**Decompose the latency.** With distributed tracing this is one span diagram and the answer is often immediate. Without it, timing instrumentation at the component boundaries is worth adding before guessing. The typical distribution of blame in a service like this: a downstream call that got slower; a query whose plan changed because the data did; a cache whose hit rate fell; serialization cost that grew with message size; lock contention that only shows above a throughput threshold; and allocation pressure, which is invisible in the business logic and very visible in a profile ([CPP-078](<../Technical Interview/C++ Core Questions.md#question-cpp-078>)).

**Then, and only then, the local tools.** `perf top` for a quick look at where cycles go, `perf record`/`report` and a flame graph for a proper profile, `perf stat` counters when the suspicion is cache or branch behaviour, and `strace` when the suspicion is syscalls or blocking I/O. If the service is multi-threaded and the profile looks flat, that itself is evidence - flat profiles with poor scaling usually mean contention or false sharing rather than a hot function ([CPP-068](<../Technical Interview/C++ Core Questions.md#question-cpp-068>), [CPP-084](<../Technical Interview/C++ Core Questions.md#question-cpp-084>)).

**Close the loop.** Re-measure after the change, against the same metric and percentile, and keep the baseline so the claim is falsifiable. And check that the fix moved the bottleneck rather than moving the time somewhere less visible ([CPP-071](<../Technical Interview/C++ Core Questions.md#question-cpp-071>)).

**Evidence boundary:** my performance work has been measurement-first optimization against a stated budget, plus native debugging with gdb, core dumps and Valgrind in the project toolchain. I have not run a sampling profiler against a live production service, and I would say so rather than let a follow-up establish it.

[↑ Back to question index](#question-index)

---

## Question ROLE-009

[↑ Back to question index](#question-index)

### Question ROLE-009 — A C++ service leaks in production but not in staging. What do you do?

**Short answer**

- Establish first whether it is a leak at all: growing RSS with a flat allocation count is fragmentation or allocator behaviour, not a leak, and the two have completely different fixes.
- Then find what production has that staging does not - traffic mix, message sizes, error paths, connection churn, uptime - because "not in staging" is the most informative part of the report.
- Get the evidence rather than reasoning about it: two heap snapshots an hour apart and their difference, or a sanitizer or heap profiler build running on one instance under real traffic.

**Details and nuances**

**Separate the three things that look identical from outside.** A true leak is an allocation count that rises without a matching free. Fragmentation is a matched count with rising RSS, because freed blocks of one size class cannot serve requests of another - a very plausible outcome in a service handling variable-sized messages. And an unbounded cache or queue is not a leak at all: the memory is reachable, correctly owned and will never be released, which no leak detector will report. That third case is the most common in message-driven services, and the tell is that growth correlates with throughput rather than with uptime.

**Interrogate the difference from staging, because it is the actual clue.** Production has real message sizes and a real protocol mix; it has error paths that staging never exercises, and leaks live on error paths more often than on happy paths; it has connection churn, reconnects and counterparty timeouts; it runs for weeks rather than hours, so a small per-request leak only becomes visible at scale; and it may have a different allocator or different tuning. Reproducing means making staging look like production in whichever of those dimensions matters - usually by replaying captured production traffic, including the malformed and timed-out parts.

**Instrument for the trend before chasing the cause** ([CPP-085](<../Technical Interview/C++ Core Questions.md#question-cpp-085>)): RSS, heap size, allocation and free counts, live object counts by type where you can get them, queue depths, connection counts and file descriptors. Those distinguish the three cases above within one observation window and cost nothing to leave running.

**Then take real evidence.** Heaptrack or Massif on one instance; ASan's leak detector or standalone LSan on a canary if the overhead is acceptable; jemalloc or tcmalloc heap profiling, which is designed to run in production and is usually the right answer for a long-running service; or two snapshots and a diff of what grew. Sanitizers are for CI and for a canary, not for the fleet ([C-020](<../Technical Interview/C Language Questions.md#question-c-020>)).

**The C++-specific suspects worth naming:** a `shared_ptr` cycle, which no leak detector reports because the memory is still reachable ([CPP-035](<../Technical Interview/C++ Core Questions.md#question-cpp-035>)); a callback or completion handler capturing `shared_from_this` that is never invoked because the operation was cancelled; an unbounded queue behind a consumer that has fallen behind ([CPP-077](<../Technical Interview/C++ Core Questions.md#question-cpp-077>)); a cache with no eviction policy; and an early return on an error path that skips a cleanup that RAII should have owned in the first place.

**And state the fix at the right level.** If the cause is a missing release on an error path, the durable fix is not adding the release - it is removing the raw ownership so the path cannot exist. If the cause is an unbounded buffer, the fix is a bound and a drop or backpressure policy, which is a design decision someone has to make explicitly rather than a bug to patch.

**Evidence boundary:** the long-running-fault reasoning is prepared and is in the C++ bank. What is real experience is native failure diagnosis on Linux - gdb and core dumps over SSH on the project's AWS hosts, and Valgrind in the toolchain - on a platform where crashes from invalid or uninitialised memory were a recurring defect class ([Story 1](<./Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading.md>)).

[↑ Back to question index](#question-index)

---

## Question ROLE-010

[↑ Back to question index](#question-index)

### Question ROLE-010 — How would you design the integration service bridging to an external trading system?

**Short answer**

- Its job is translation and isolation: the external protocol and its quirks stop at this service, and the rest of the platform sees only the internal model.
- Design it around the external system being unreliable and slow to change - session management, reconnection, sequence gaps, retries, rate limits and a circuit breaker are the substance of the service, not extras.
- Everything crossing the boundary needs an identity, so that a retry is recognisable, a reply can be matched to its request, and a reconciliation is possible after any disconnection.

**Details and nuances**

**The isolation rule is the whole architecture.** If the external system's field names, enumerations, error codes or oddities leak into the internal model, every future integration inherits them and the model becomes the union of everyone's quirks. One adapter per external system, translating both ways, with the internal model owned by the domain and not by any counterparty. The test of whether you got it right is whether adding a second venue requires changing anything outside its own adapter.

**Session and transport are real work.** A FIX session, for example, is not a stateless request channel: it has logon and logout, heartbeats and test requests, and monotonically increasing sequence numbers in each direction, so a gap triggers a resend request and a recovery procedure rather than a reconnect-and-forget. Sequence numbers persist across a reconnect - that is the point of them - which means the service has durable session state and a start-of-day or reset procedure. Any proprietary API has a less formal version of the same problems.

**Assume the far side fails in every way.** Slow responses, no response, a response after you gave up, malformed messages, an unexpected disconnect mid-negotiation, and a maintenance window. The mechanisms are standard and should be named as a set: timeouts on every call, retries with backoff and jitter *only where the operation is idempotent* ([ROLE-006](#question-role-006)), a circuit breaker so a dead counterparty degrades one venue rather than saturating the platform's threads, rate limiting on the outbound side because most venues enforce one, and a bounded queue with an explicit policy for what happens when it fills ([CPP-077](<../Technical Interview/C++ Core Questions.md#question-cpp-077>)).

**Reconciliation is not optional in this domain.** After any disconnection the two sides may disagree about what happened during the gap, and the resolution cannot be a guess: on reconnect, query the external system's view of open state and compare it with ours, then alert on differences rather than silently adopting either side. A trading integration that cannot answer "what does the counterparty think is still live" after a network partition is incomplete.

**Observability specific to a boundary:** per-venue metrics - connection state, round-trip latency, message rates in and out, rejection counts by reason, sequence gaps and resends - plus the full message log, which in this domain is usually a compliance requirement as well as a debugging one. The correlation identifier has to survive the translation in both directions, or a cross-boundary investigation becomes guesswork ([ROLE-005](#question-role-005)).

**And the deployment consequence:** because this service holds session state, it is the one that cannot simply be restarted during trading hours ([ROLE-012](#question-role-012)).

**Evidence boundary:** the trading protocols are prepared knowledge. The transferable experience is protocol integration where the library owned the session and the application had to observe rather than duplicate its state - SIP registration, failover and recovery on the phone platform - and cross-boundary debugging on a platform whose components spoke custom inter-service protocols.

[↑ Back to question index](#question-index)

---

## Question ROLE-011

[↑ Back to question index](#question-index)

### Question ROLE-011 — How do you test a service whose behaviour depends on other services?

**Short answer**

- Push as much as possible below the network: a state machine and its transitions are pure logic and should be unit-testable with no transport at all, which is a design property to enforce rather than a testing technique.
- For the boundaries that remain, prefer a fake that implements the counterparty's contract over a mock that asserts on calls, because a fake survives refactoring and a mock encodes the current implementation.
- Contract tests keep the fake honest, and the small set of things that only an integrated environment can prove - session handling, sequence recovery, reconnection, schema compatibility across versions - is where the expensive tests belong.

**Details and nuances**

**The layering that makes this tractable:**

| Level | What it proves | What it must not need |
|---|---|---|
| Unit | transition logic, validation, protocol policy, serialization round trips | any network, clock or thread |
| Component | the service against fakes of its dependencies | real counterparties |
| Contract | that the fakes still match the real interfaces | a full environment |
| Integration | session recovery, gap handling, schema compatibility, deploy behaviour | — |

The inversion to avoid is the familiar one: when the unit level is thin because the logic is entangled with I/O, every regression has to be caught by a slow, flaky integration test, and eventually nobody trusts the suite ([TEST-004](<../Technical Interview/Testing Questions.md#question-test-004>)).

**Fakes over mocks, and the reason matters.** A mock asserts that a particular call was made with particular arguments, which couples the test to the implementation - refactor the implementation and the test fails without anything being broken. A fake is a working in-memory implementation of the counterparty's contract, so the test asserts on outcomes and survives the refactor. Mocks earn their place for interaction-based assertions that genuinely are the requirement - "the cancel was sent exactly once" - and not as the default ([TEST-010](<../Technical Interview/Testing Questions.md#question-test-010>)).

**The fake drifting from reality is the standard failure of this approach**, and contract testing is the answer: a shared set of cases run against both the fake and the real implementation, so a change to the real one that the fake does not reflect fails a test rather than surviving to production. With schema-driven interfaces there is a second, cheaper layer of the same protection - a compatibility check in CI against the previously released schema ([ROLE-002](#question-role-002)).

**Time and concurrency must be injected, not waited for.** An expiry test that sleeps is slow and flaky; the same test with an injected clock is deterministic and runs in microseconds ([TEST-013](<../Technical Interview/Testing Questions.md#question-test-013>)). For concurrency, the honest position is that tests demonstrate absence of a race only weakly, so the real tools are ThreadSanitizer in CI and designs that make the race impossible - single ownership per line, message passing instead of shared mutable state ([TEST-012](<../Technical Interview/Testing Questions.md#question-test-012>), [CPP-053](<../Technical Interview/C++ Core Questions.md#question-cpp-053>)).

**What only integration proves** is worth enumerating, because it justifies the cost: a session recovering across a real disconnection with real sequence numbers; two service versions interoperating during a rolling deploy; a schema change behaving as the compatibility rules promise; and the behaviour of the whole path under a burst. Everything else should have been caught earlier and faster.

**Evidence boundary:** I have written focused unit suites with Qt Test and MSTest and worked alongside a separate QA organisation, and on the library platform validation happened on shared Linux environments rather than locally. I have not owned a contract-testing setup, and would describe it as something I would argue for rather than something I have run.

[↑ Back to question index](#question-index)

---

## Question ROLE-012

[↑ Back to question index](#question-index)

### Question ROLE-012 — How do you deploy a change to a service that is holding live trading state?

**Short answer**

- Decide first whether the state can be externalised, because a stateless service is a solved deployment problem and a stateful one is not - and if the state must live in the process, the deploy has to preserve or recover it.
- A rolling deploy means two versions run at once, so every change must be compatible in both directions on the wire and in persistence before it can be rolled at all.
- The mechanics that make it safe are draining rather than killing, a canary before the fleet, a rollback that does not depend on the new code, and a maintenance window for the changes that genuinely cannot be done live.

**Details and nuances**

**Externalise what you can.** State held in a database, a cache or an event log makes the process replaceable, which turns the hardest part of the problem into an infrastructure concern that is already solved. State that must stay in the process - an open session, an in-flight negotiation, a sequence number - is what makes the deploy hard, and it is worth knowing exactly which state is in that category before designing anything.

**Two versions will coexist, so plan for it.** During a rolling update, instance A on the old version and instance B on the new one exchange messages and read each other's persisted rows. Every change therefore has to be both backward and forward compatible at the moment it is deployed, and anything that is not must be split into the phased sequence: deploy the code that understands both, then the code that uses the new form, then remove the old ([ROLE-002](#question-role-002)).

**Draining, not stopping.** On shutdown: stop accepting new work, finish or hand over what is in flight, flush and persist, then exit - and let the orchestrator's termination grace period actually accommodate that, because a `SIGKILL` thirty seconds in makes the graceful path decorative. Readiness and liveness are different signals and conflating them is a classic incident: readiness should go false while the instance drains, so traffic stops arriving, while liveness stays true so nothing kills it mid-drain ([CNT-011](<../Technical Interview/Containers and Orchestration Questions.md#question-cnt-011>), [CNT-012](<../Technical Interview/Containers and Orchestration Questions.md#question-cnt-012>)).

**Sessions to external systems are the special case.** A FIX session's sequence numbers are durable state shared with a counterparty, so restarting the process is a protocol event, not just an operational one - the session must be re-established and any gap resolved through the protocol's own resend mechanism. That is usually why an integration service is deployed outside trading hours while the stateless services roll continuously ([ROLE-010](#question-role-010)).

**Make rollback a real plan rather than a hope.** A canary instance carrying a small share of traffic, with a decision made on metrics rather than on absence of alarms; a rollback path that does not require the new code to run - which is why a database migration that drops a column is a one-way door and an additive one is not; and a feature flag separating deploy from release, so the new behaviour can be turned off without another deploy.

**Say plainly what cannot be done live.** Some changes - a persistence format that cannot be read both ways, a protocol version bump negotiated with a counterparty, an incompatible framework upgrade - need a maintenance window. Recognising that and scheduling it is better engineering than a clever mechanism that half works during the trading day.

**Evidence boundary:** this is prepared knowledge plus adjacent experience. What I have actually lived is the other extreme of the same tradeoff: a platform releasing roughly twice a year, where a defect that shipped stayed in front of nine thousand-plus library customers until the next release. That cadence taught me to think about what is reversible and what is not, which is the same question a rolling deploy asks at a much shorter timescale ([Story 1](<./Vacancy Preparation. FinDev Senior C++ Engineer, Electronic Bond Trading.md>)).

[↑ Back to question index](#question-index)
