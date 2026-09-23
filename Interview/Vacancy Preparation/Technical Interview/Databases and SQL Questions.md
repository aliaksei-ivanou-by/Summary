# Databases and SQL Technical Interview Questions and Answers

> Reusable bank for the database questions an application engineer is asked - schema, indexes, query plans, transactions, and the difference between an embedded and a server database. Written from the application side, because that is where my experience is: consuming and debugging databases rather than administering them.

**Scope of my own claims.** Application-level integration with MySQL, PostgreSQL and SQLite: writing and debugging queries, tracing a value from application state into a stored record and back, and using SQLite through the C API with hand-written prepared statements. Not schema ownership, not performance tuning as a role, not administration. State that boundary rather than letting the list of three engines imply more.

# Question Index

## Schema and Modelling (DB-001–DB-004)

- [DB-001. What are primary and foreign keys, and what does a foreign key actually enforce?](#question-db-001)
- [DB-002. What is normalization, and when would you denormalize?](#question-db-002)
- [DB-003. `NULL` - what makes it different from every other value?](#question-db-003)
- [DB-004. Which JOIN types are there, and which one do people get wrong?](#question-db-004)

## Indexes and Query Plans (DB-005–DB-008)

- [DB-005. How does an index work, and what does it cost?](#question-db-005)
- [DB-006. When will an index not be used?](#question-db-006)
- [DB-007. A query is slow. What do you do?](#question-db-007)
- [DB-008. What is the N+1 query problem?](#question-db-008)

## Transactions and Engines (DB-009–DB-012)

- [DB-009. What does ACID mean in practice?](#question-db-009)
- [DB-010. What are isolation levels, and which anomalies do they allow?](#question-db-010)
- [DB-011. What is a deadlock in a database, and how do you avoid one?](#question-db-011)
- [DB-012. SQLite vs a server database - when is each right?](#question-db-012)

---

# 1. Schema and Modelling

## Question DB-001

[↑ Back to question index](#question-index)

### Question DB-001 — What are primary and foreign keys, and what does a foreign key actually enforce?

**Short answer**

- A primary key uniquely identifies a row, is never null, and in most engines creates a unique index automatically.
- A foreign key says a column's values must exist as a key in another table - referential integrity enforced by the database rather than hoped for by the application.
- It also defines what happens when the referenced row is deleted or updated: `RESTRICT`, `CASCADE` or `SET NULL`, and choosing that is a design decision, not a default to accept.

**Details and nuances**

The argument worth having ready is why the constraint belongs in the database at all: every application that touches the data would otherwise have to enforce it, and one that forgets leaves orphan rows that the others then have to tolerate forever. A constraint is enforced once, by the only component that sees every write.

`ON DELETE CASCADE` deserves care - it is convenient and it means a single delete can silently remove a great deal. `RESTRICT` forces the application to be explicit about what it is destroying, which on data a user cares about is usually the safer default.

Natural versus surrogate keys is the related question: a surrogate key (an integer or UUID) is stable when the business meaning changes, which natural keys rarely are - an email address or a national identifier both look unique until the day one has to change.

[↑ Back to question index](#question-index)

---

## Question DB-002

[↑ Back to question index](#question-index)

### Question DB-002 — What is normalization, and when would you denormalize?

**Short answer**

- Normalization removes redundancy by splitting data so each fact is stored once: first normal form for atomic values, second for no partial dependency on part of a key, third for no dependency between non-key columns.
- The benefit is that an update happens in one place, so the data cannot contradict itself. The cost is more joins to read it back.
- Denormalize deliberately, after measuring, when read cost dominates - and accept that you have taken on the job of keeping the duplicates consistent.

**Details and nuances**

The trade-off stated as a rule: normalization optimises for correctness of writes, denormalization for speed of reads. Most application databases should start normalized, because a correctness problem is discovered by a user and a performance problem is discovered by a profiler.

When denormalizing, the question to answer first is who keeps the copies in agreement - a trigger, the application, or a periodic job - because "we will remember to update both" is not an answer, and the failure is silent divergence rather than an error.

[↑ Back to question index](#question-index)

---

## Question DB-003

[↑ Back to question index](#question-index)

### Question DB-003 — `NULL` - what makes it different from every other value?

**Short answer**

- `NULL` means unknown, not zero and not an empty string, so comparisons with it yield unknown rather than true or false: `NULL = NULL` is not true, and `IS NULL` is the only way to test it.
- That propagates: `WHERE x != 'a'` excludes rows where `x` is `NULL`, which is almost never what the author intended.
- Aggregates skip nulls - `COUNT(col)` counts non-null values while `COUNT(*)` counts rows - and that difference is a classic source of a wrong number in a report.

**Details and nuances**

The three-valued logic is the part to be able to state cleanly, because it explains every surprising result at once: predicates keep rows only when they evaluate to true, and unknown is not true.

Practical consequences: `NOT IN` with a subquery that can return `NULL` returns no rows at all, which looks like a broken query; `COALESCE(x, default)` is how you get a concrete value; and a unique constraint typically permits multiple nulls, because two unknowns are not known to be equal.

**Example or evidence boundary**

Adjacent production experience: on the EPAM sensor-data product a defect regularly meant a value that was correct in one representation and wrong in another, and tracing it through parsing, MySQL persistence and the user-facing layer is where this kind of detail decides where the fault actually is.

[↑ Back to question index](#question-index)

---

## Question DB-004

[↑ Back to question index](#question-index)

### Question DB-004 — Which JOIN types are there, and which one do people get wrong?

**Short answer**

- `INNER JOIN` keeps only matching rows; `LEFT JOIN` keeps every row from the left side with nulls where there is no match; `RIGHT` is the mirror; `FULL OUTER` keeps both sides; `CROSS` is the cartesian product.
- The one people get wrong is a `LEFT JOIN` with a condition on the right table in the `WHERE` clause - that filters the nulls back out and silently turns it into an inner join.
- The fix is to put the condition in the `ON` clause, where it restricts the match rather than the result.

**Details and nuances**

```sql
-- Not what was meant: rows with no order disappear
SELECT c.id, o.total FROM customers c
LEFT JOIN orders o ON o.customer_id = c.id
WHERE o.status = 'paid';

-- Intended: every customer, with paid orders where they exist
SELECT c.id, o.total FROM customers c
LEFT JOIN orders o ON o.customer_id = c.id AND o.status = 'paid';
```

The second thing worth knowing is accidental multiplication: joining a table that has several matching rows multiplies the left side, so a `SUM` over the result is silently too large. A row count before and after the join is the quickest check that the join did what you thought.

[↑ Back to question index](#question-index)

---

# 2. Indexes and Query Plans

## Question DB-005

[↑ Back to question index](#question-index)

### Question DB-005 — How does an index work, and what does it cost?

**Short answer**

- Usually a B-tree keyed on the indexed columns with a pointer to the row, so a lookup is logarithmic rather than a full scan - and the tree is ordered, so it also serves range queries and `ORDER BY`.
- The cost is on writes: every insert, update and delete has to maintain every index on the table, plus the storage.
- So indexes are not free and not automatic: index what queries actually filter and sort on, and remove indexes nothing uses.

**Details and nuances**

Composite indexes have a rule worth stating exactly: an index on `(a, b, c)` serves queries filtering on `a`, on `a, b`, or on `a, b, c` - the leftmost prefix - but not a query filtering only on `b`. Column order is therefore a design decision, not a formatting one.

A covering index is the other idea to have ready: if the index contains every column the query needs, the engine answers from the index alone without touching the row - visible in a plan as an index-only scan, and often the difference between slow and instant.

[↑ Back to question index](#question-index)

---

## Question DB-006

[↑ Back to question index](#question-index)

### Question DB-006 — When will an index not be used?

**Short answer**

- When the column is wrapped in a function or an expression - `WHERE YEAR(created) = 2026` cannot use an index on `created`, while `WHERE created >= '2026-01-01' AND created < '2027-01-01'` can.
- When a leading wildcard makes it unusable: `LIKE '%foo'` cannot use a B-tree, while `LIKE 'foo%'` can.
- When the planner decides a scan is cheaper - because the query matches most of the table, or because the statistics are stale and it believes the wrong thing.

**Details and nuances**

The last case is the interesting one, because it is not a mistake: reading 60% of a table through an index means random access for most rows, and a sequential scan really is faster. The planner is doing cost estimation, and it is right more often than the person overriding it.

Stale statistics are the failure mode to name: after a bulk load the planner's idea of the data can be badly wrong, and `ANALYZE` fixes what looks like an inexplicable plan regression.

Also worth mentioning: an implicit type conversion - comparing a string column to a number - disables the index just as a function would, and the query still returns correct results, so nothing looks wrong except the time.

[↑ Back to question index](#question-index)

---

## Question DB-007

[↑ Back to question index](#question-index)

### Question DB-007 — A query is slow. What do you do?

**Short answer**

- Read the plan first - `EXPLAIN`, or better `EXPLAIN ANALYZE` in PostgreSQL, which runs it and reports actual rows against estimated. Do not guess and do not start adding indexes.
- Look for the specific things: a sequential scan on a large table, a row estimate wildly different from the actual count, a nested loop over many rows, or a sort that spills to disk.
- Then fix the cause: an index, a rewritten predicate that is index-usable, fewer columns, a smaller result, or refreshed statistics - and measure again.

**Details and nuances**

The estimate-versus-actual comparison is the single most useful thing in a plan, because a planner that thinks a step returns 10 rows when it returns 100,000 will choose a strategy that is correct for 10 and catastrophic for 100,000. That mismatch points at statistics or at a predicate the planner cannot reason about, and it explains the plan rather than just describing it.

Before optimising the query, it is worth asking whether the query is the problem at all: the application may be running it a thousand times ([DB-008](#question-db-008)), or asking for a hundred thousand rows to display twenty.

**Example or evidence boundary**

Prepared knowledge for plan-level tuning. My production experience is application-side: inspecting stored state to determine whether a value was written or read incorrectly, not owning query performance.

[↑ Back to question index](#question-index)

---

## Question DB-008

[↑ Back to question index](#question-index)

### Question DB-008 — What is the N+1 query problem?

**Short answer**

- One query fetches N rows, and then the code runs one more query per row - N+1 round trips where one or two would do.
- It is usually invisible in the code, because the per-row query is hidden behind a property access or a lazy-loaded relation, and it scales linearly with data so it passes every test with ten rows.
- Fix by fetching what is needed in one query - a join, or a second query with `WHERE id IN (...)` - rather than by making the individual query faster.

**Details and nuances**

The reason it deserves its own name is that the cost is per round trip, not per row of data: a query taking two milliseconds is fine, and a thousand of them is two seconds of latency during which the application is doing nothing. The fix is structural and the profile does not point at any single slow query, which is why it is often found by counting queries rather than by timing them.

That generalises beyond databases - it is the same shape as per-cell COM access across a process boundary, or a `context.sync()` inside a loop in Office.js. The cost is the crossing.

[↑ Back to question index](#question-index)

---

# 3. Transactions and Engines

## Question DB-009

[↑ Back to question index](#question-index)

### Question DB-009 — What does ACID mean in practice?

**Short answer**

- **Atomicity**: a transaction happens entirely or not at all. **Consistency**: it moves the database from one valid state to another, respecting constraints. **Isolation**: concurrent transactions do not see each other's partial work. **Durability**: once committed, it survives a crash.
- The practical consequence is that multi-step changes belong in one transaction - debit and credit, insert and update-counter - so a failure between steps cannot leave half of it.
- The one that is negotiable is isolation: engines offer levels, and the level chosen decides which anomalies are possible ([DB-010](#question-db-010)).

**Details and nuances**

Durability is worth one extra sentence because it is where databases differ in ways that matter: it is guaranteed by a write-ahead log flushed to disk at commit, and the settings that make commits faster - group commit, relaxed flushing - trade exactly that guarantee. Anyone who has turned `synchronous_commit` off has accepted losing the last fraction of a second on a power failure, and should know they did.

On the application side the most common mistake is a transaction held open too long - across a network call or a user interaction - which holds locks for a human-scale duration and turns a correctness mechanism into a contention problem.

[↑ Back to question index](#question-index)

---

## Question DB-010

[↑ Back to question index](#question-index)

### Question DB-010 — What are isolation levels, and which anomalies do they allow?

**Short answer**

- Four standard levels, each ruling out more: Read Uncommitted allows dirty reads; Read Committed prevents those but allows non-repeatable reads; Repeatable Read also prevents those but classically allows phantoms; Serializable behaves as though transactions ran one at a time.
- The trade is concurrency: stricter isolation means more locking or more aborted transactions, so the right level is the weakest one your invariants tolerate.
- Defaults differ, which is a real portability trap: PostgreSQL defaults to Read Committed, MySQL's InnoDB to Repeatable Read.

**Details and nuances**

| Level | Dirty read | Non-repeatable read | Phantom |
|---|---|---|---|
| Read Uncommitted | possible | possible | possible |
| Read Committed | no | possible | possible |
| Repeatable Read | no | no | possible (prevented by InnoDB and by PostgreSQL's snapshot) |
| Serializable | no | no | no |

The definitions in one line each: a dirty read sees uncommitted data; a non-repeatable read gets a different value for the same row twice in one transaction; a phantom is a new row appearing in a repeated range query.

Worth adding that real engines are not the textbook: PostgreSQL implements Repeatable Read as snapshot isolation and does not exhibit phantoms, and InnoDB uses next-key locking for the same purpose. So "which anomalies can happen" is a question about the engine, not only about the level name.

[↑ Back to question index](#question-index)

---

## Question DB-011

[↑ Back to question index](#question-index)

### Question DB-011 — What is a deadlock in a database, and how do you avoid one?

**Short answer**

- Two transactions each hold a lock the other needs, so neither can proceed; unlike an application deadlock, the database detects it and aborts one transaction with an error rather than hanging forever.
- Avoid it by acquiring locks in a consistent order everywhere, keeping transactions short, and touching the fewest rows you can.
- Because the engine resolves it by aborting, the application must handle that error by retrying - a deadlock is an expected condition under concurrency, not a bug to be eliminated.

**Details and nuances**

The ordering rule is the one with real content: if every code path updates parent before child, or always ascends by primary key, the cycle cannot form. Deadlocks in practice usually come from two code paths written months apart that happen to touch the same two tables in opposite orders.

The retry must be at the transaction level, not the statement level - the whole transaction was rolled back, so re-running the last statement is meaningless - and it needs a bounded count so a persistent conflict does not become an infinite loop.

This is the same reasoning as the C++ lock-ordering answer; the difference is only that the database detects the cycle and a mutex does not.

[↑ Back to question index](#question-index)

---

## Question DB-012

[↑ Back to question index](#question-index)

### Question DB-012 — SQLite vs a server database - when is each right?

**Short answer**

- SQLite is a library, not a server: the database is one file in your process, with no network, no configuration and no administration - which makes it right for embedded devices, desktop applications, local caches and test fixtures.
- A server database is right when several processes or machines need concurrent write access, when you need real user management and network access, or when the data outgrows one machine's care.
- The dividing line is usually concurrency and operations rather than data size: SQLite handles large files well and multiple concurrent writers badly.

**Details and nuances**

SQLite's concurrency model is worth being precise about: many readers, one writer. In WAL mode readers do not block the writer and the writer does not block readers, which removes most of the pain, but two simultaneous writers still means one waits or gets `SQLITE_BUSY` - so a busy timeout is not optional in any real application.

Two more details that come up: SQLite has dynamic typing with type affinity rather than strict column types, so a string can land in an `INTEGER` column unless `STRICT` tables are used; and foreign keys are off by default and need `PRAGMA foreign_keys = ON` per connection, which surprises people who assumed the constraint was being enforced.

**Example or evidence boundary**

Production experience: SQLite through the C API with hand-written prepared statements in my own C++20 application - a per-entity data-access layer over the raw sqlite3 interface rather than an ORM - and SQLite in the device-provisioning workflows on the embedded platform.

[↑ Back to question index](#question-index)
