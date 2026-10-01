# Databases and SQL Assessment — Answer Guide

This handbook covers relational modelling, SQL, query performance, transactions, and database integration from C++. Every question has a model answer. SQL syntax and isolation details vary by engine; strong answers state the invariant and verify dialect-specific behavior rather than assuming all databases are identical.

Labels:

- **[Basic]** — expected foundations.
- **[Deep dive]** — optimizer, concurrency, or modelling nuance.
- **[Code]** — query or integration exercise.
- **[Design]** — schema, consistency, or operational choice.

## Contents

1. [Relational modelling and schemas](#1-relational-modelling-and-schemas)
2. [SQL querying and data modification](#2-sql-querying-and-data-modification)
3. [Indexes and query plans](#3-indexes-and-query-plans)
4. [Transactions and concurrency](#4-transactions-and-concurrency)
5. [C++ integration and database operations](#5-c-integration-and-database-operations)

---

# 1. Relational modelling and schemas

1. **[Basic] What is the relational model?**

   **Answer.** It represents data as relations (tables) of tuples (rows) over named attributes (columns), with keys, constraints, and operations grounded in relational algebra. Logical relationships are expressed by values and constraints rather than in-memory pointers. SQL databases add practical features and sometimes bags/duplicates, nulls, ordering clauses, procedural extensions, and engine-specific types beyond the pure model.

2. **[Basic] What do primary, unique, and foreign keys enforce?**

   **Answer.** A primary key uniquely identifies each row and is non-null; a table has one declared primary key, possibly composite. A unique constraint enforces uniqueness for another candidate key, with engine-specific null treatment. A foreign key requires referenced values to identify an existing parent key (or be null when allowed) and defines update/delete actions. It enforces referential existence, not arbitrary business rules or automatic object loading.

3. **[Design] Natural key or surrogate key—which should be used?**

   **Answer.** A natural key has domain meaning, such as an externally stable code; it can prevent duplicates directly but may be wide, mutable, privacy-sensitive, or poorly controlled. A surrogate key is generated and stable/compact but does not prevent duplicate domain identities by itself, so a unique natural constraint may still be needed. Choose using stability, ownership, join width, distribution, and exposure; avoid assuming every human-visible identifier is immutable.

4. **[Basic] What is normalization?**

   **Answer.** Normalization decomposes data so each fact is represented in an appropriate relation and update/insert/delete anomalies are reduced. First normal form requires atomic values under the chosen model; higher normal forms address functional dependencies, partial/transitive dependencies, and more subtle join dependencies. Interviews commonly focus on 1NF–3NF/BCNF. The goal is integrity and clear ownership of facts, not maximizing table count.

5. **[Design] When is denormalization justified?**

   **Answer.** Duplicate or precompute data when measured read latency/availability requirements cannot be met economically through indexes, query design, or caching, and when update consistency is explicitly managed. Examples include materialized views, search indexes, aggregates, and read models. Define the source of truth, refresh/repair path, staleness contract, and monitoring. Accidental duplicated columns with no reconciliation are corruption waiting to happen.

6. **[Basic] Why is SQL `NULL` different from every ordinary value?**

   **Answer.** It represents missing/unknown/not-applicable under a three-valued logic. Comparisons such as `x = NULL` evaluate to unknown rather than true; use `IS NULL`/`IS NOT NULL`. `WHERE` keeps only true rows, while constraints, uniqueness, aggregates, ordering, and joins have dialect-specific null semantics. `COUNT(column)` excludes nulls; `COUNT(*)` counts rows. Model distinct meanings separately when “unknown” and “not applicable” require different behavior.

7. **[Deep dive] Why should integrity be enforced with database constraints as well as application checks?**

   **Answer.** Application checks can race with concurrent writers and may be bypassed by another service, script, or migration. `NOT NULL`, `CHECK`, unique, foreign-key, and exclusion-like constraints let the database reject invalid committed state atomically. Applications still validate for good user errors, but correctness should not depend on a read-then-write race. Constraints must reflect real semantics and be deployed/migrated carefully on existing data.

8. **[Design] How should SQL data types be selected?**

   **Answer.** Choose types that encode the domain and required range/precision: fixed-precision decimal or integral minor units for money, timezone-aware/defined timestamp semantics, bounded text only when the bound is meaningful, and binary/UUID types when supported. Avoid storing numbers/dates/JSON structures as arbitrary text. Consider comparison/collation, indexing, storage, driver mapping, overflow, and compatibility before choosing vendor-specific types.

9. **[Deep dive] What are generated columns, views, and materialized views?**

   **Answer.** A generated/computed column derives a value from the row and may be virtual or stored/indexable depending on the engine. A view stores a query definition and computes results when referenced; it can provide abstraction/security but does not inherently improve speed. A materialized view stores query results and must be refreshed, trading staleness/update work for faster reads. Optimizer support and update semantics vary.

10. **[Design] How should ownership and lifecycle be modelled for parent/child rows?**

    **Answer.** Decide whether the child is an independent entity or exists only within the parent's aggregate. Use foreign keys and an explicit delete action: restrict for protected dependents, cascade only when lifecycle ownership is real, or set-null for optional relationships. Cascades can touch many rows and surprise audit/recovery processes. Soft deletion requires every uniqueness/query relationship to define whether deleted rows still participate.

---

# 2. SQL querying and data modification

1. **[Basic] What is the logical processing order of a `SELECT` query?**

   **Answer.** Conceptually it is `FROM`/joins, `WHERE`, grouping, aggregate evaluation, `HAVING`, select-list expressions, `DISTINCT`, ordering, then limit/offset (with dialect nuances). This explains why a select alias often cannot be used in `WHERE` and why filtering before grouping differs from `HAVING`. The optimizer may execute a physically different plan as long as results follow SQL semantics.

2. **[Basic] Explain `INNER`, `LEFT`, `RIGHT`, `FULL`, and `CROSS` joins.**

   **Answer.** Inner join returns matching pairs. Left join returns all left rows plus matches, filling unmatched right columns with null; right is symmetric. Full outer join preserves unmatched rows from both sides. Cross join forms the Cartesian product. Support varies by engine. The most common mistake is accidentally turning an outer join into an inner join by filtering nullable right-side columns in `WHERE` instead of placing the intended condition in `ON`.

3. **[Code] Why do these two outer-join queries differ?**

   ```sql
   SELECT c.id, o.id
   FROM customer c
   LEFT JOIN orders o ON o.customer_id = c.id
   WHERE o.status = 'open';

   SELECT c.id, o.id
   FROM customer c
   LEFT JOIN orders o
     ON o.customer_id = c.id AND o.status = 'open';
   ```

   **Answer.** The first removes rows whose `o.status` is null after the join, so customers without an open order disappear and the result behaves like an inner join for that predicate. The second restricts which orders match while preserving every customer with null right-side columns when no open order exists. Choose based on whether absence should remain in the result.

4. **[Basic] How do `GROUP BY`, aggregate functions, and `HAVING` work?**

   **Answer.** Grouping partitions input rows by key and computes aggregates such as `COUNT`, `SUM`, `MIN`, or `AVG` per group. `WHERE` filters input rows before aggregation; `HAVING` filters groups after aggregation. Every selected non-aggregate expression must be functionally determined by the group under the dialect's rules. Aggregates generally ignore null inputs except `COUNT(*)`; empty-input results need explicit handling.

5. **[Basic] What are window functions?**

   **Answer.** A window function computes across related rows while retaining one output row per input, unlike `GROUP BY`. `OVER (PARTITION BY ... ORDER BY ... ROWS/RANGE ...)` defines partitions, order, and frame. Uses include ranking, running totals, moving averages, gaps, and comparing with `LAG`/`LEAD`. Default frames can surprise when peers share the ordering value, so specify frames when exact row behavior matters.

6. **[Code] How would you select the latest row per entity?**

   **Answer.** A portable pattern ranks rows per entity, with a deterministic tie-breaker, then selects rank one. An index beginning with `(entity_id, observed_at DESC, id DESC)` may help. Vendor-specific `DISTINCT ON`, lateral joins, or aggregate-join techniques can be faster but require careful tie semantics.

   ```sql
   WITH ranked AS (
     SELECT e.*,
            ROW_NUMBER() OVER (
              PARTITION BY entity_id
              ORDER BY observed_at DESC, id DESC) AS rn
     FROM event e
   )
   SELECT * FROM ranked WHERE rn = 1;
   ```

7. **[Basic] What are CTEs, and are they automatically materialized?**

   **Answer.** A common table expression names a subquery for the following statement and can improve decomposition/readability; recursive CTEs iterate a seed and recursive term for trees/graphs. Materialization is engine/version/query dependent: some optimizers inline ordinary CTEs, others treat them as an optimization fence or allow explicit control. Use the execution plan rather than assuming a CTE is a temporary table or “only runs once.”

8. **[Deep dive] `EXISTS` versus `IN` versus a join—how should they be chosen?**

   **Answer.** `EXISTS` naturally expresses whether at least one correlated row exists and avoids duplicate multiplication. `IN` expresses membership but `NOT IN` is dangerous if the subquery can contain null, often yielding unknown for every candidate; `NOT EXISTS` is safer. A join is appropriate when columns or multiplicity from both sides are required. Optimizers may transform equivalent forms, so prioritize correct semantics and inspect plans for critical queries.

9. **[Basic] How do `UNION`, `UNION ALL`, `INTERSECT`, and `EXCEPT` differ?**

   **Answer.** They combine compatible query results as set-like operations. `UNION` removes duplicates, adding sort/hash work; `UNION ALL` preserves duplicates and is usually cheaper. `INTERSECT` retains common rows and `EXCEPT` rows from the first absent in the second, with dialect differences in availability and duplicate variants. Column types/positions must be compatible; final ordering applies to the combined result.

10. **[Deep dive] Why is offset pagination problematic, and what is keyset pagination?**

    **Answer.** Large `OFFSET` often requires scanning/skipping many rows, and concurrent inserts/deletes can cause duplicates or omissions between pages. Keyset/seek pagination uses the last seen values of a deterministic indexed order, for example `(created_at,id) < (?,?)`, to continue. It is fast and stable for forward navigation but cannot jump directly to arbitrary page numbers and must define sort direction/null/tie behavior.

11. **[Code] How should an atomic increment or conditional update be written?**

    **Answer.** Express it in one statement rather than read-modify-write in application code, optionally checking affected rows or returning the new value where supported.

    ```sql
    UPDATE account
       SET version = version + 1,
           balance = balance - :amount
     WHERE id = :id
       AND version = :expected_version
       AND balance >= :amount;
    ```

    Zero affected rows means the optimistic version/precondition failed. The enclosing transaction must still coordinate any related credit/audit entry.

12. **[Deep dive] What is an upsert, and why is it dialect-sensitive?**

    **Answer.** It atomically inserts a row or handles a uniqueness conflict by updating/ignoring according to a chosen key. PostgreSQL-style `ON CONFLICT`, MySQL-style duplicate-key syntax, SQL `MERGE`, and SQLite variants differ in matching, concurrency, trigger, and affected-row behavior. Specify the conflict constraint and desired idempotency; a generic “check then insert” races unless protected by a unique constraint and retry/transaction logic.

---

# 3. Indexes and query plans

1. **[Basic] How does a B-tree-family index work?**

   **Answer.** It stores sorted keys in a balanced, page-oriented tree with references to rows or primary keys. High fan-out keeps height small, supporting equality, ordered range, prefix, and ordered traversal in logarithmic page searches plus result scanning. Exact page/layout/concurrency details vary by engine. Indexes accelerate reads at the cost of storage, cache pressure, and maintenance on writes.

2. **[Deep dive] How does column order matter in a composite index?**

   **Answer.** An index on `(a,b,c)` is naturally ordered first by `a`, then `b` within each `a`, then `c`. It can efficiently support leading-prefix constraints/order; equality on leading columns allows later range/order use, while a range on an early column often limits use of later columns for search ordering. Optimizers can sometimes use skip scans/intersections, but design against actual important queries and plans.

3. **[Basic] What is a covering/index-only query?**

   **Answer.** If the index contains every column needed for filtering and output, the engine may avoid fetching base-table rows. Some engines require visibility checks or store included/non-key columns differently, so “covering” does not always mean zero table access. Wider indexes consume memory/storage and increase write cost. Include columns only for measured high-value queries.

4. **[Deep dive] What makes a predicate sargable?**

   **Answer.** A search-argument-able predicate lets the engine map conditions to an index range/lookup. Applying a function/cast/arithmetic to the indexed column, mismatched types/collations, leading-wildcard search, or some `OR` forms can prevent efficient use. Rewrite `DATE(ts)=:d` as a half-open timestamp range when semantically correct, or create an expression index if the engine supports it and the expression is stable.

5. **[Deep dive] Why might an optimizer ignore an available index?**

   **Answer.** The query may return a large fraction of rows, making sequential access cheaper; statistics may predict low selectivity; the predicate may be non-sargable or type-mismatched; column order may not fit; the table may be small; or sorting/table lookups may dominate. Parameter-sensitive plans and stale statistics also matter. An index being mentioned in a schema does not make it useful for every query.

6. **[Code] A query is slow. What investigation sequence should be used?**

   **Answer.** Capture the exact query, bound parameter shapes, latency distribution, returned row count, concurrency, waits, and data scale. Use the engine's actual execution plan where safe, comparing estimates with actual rows/timing; inspect scans, joins, sorts, spills, locks, and I/O. Check statistics and schema/indexes, then change one thing and remeasure correctness and load. Avoid adding indexes or hints from the SQL text alone.

7. **[Deep dive] What information appears in an execution plan?**

   **Answer.** It shows physical operators such as scans/seeks, nested-loop/hash/merge joins, sorts, aggregates, materialization, and parallel exchange, plus estimated costs/cardinalities. Analyze/actual modes may add real rows, loops, time, buffers, and spills but execute the query and can affect production. A large estimate/actual mismatch points to statistics, correlation, skew, parameter, or predicate issues and can lead to a poor join/order choice.

8. **[Basic] What is the N+1 query problem?**

   **Answer.** Code fetches N parent rows, then issues another query for each parent's related data, causing N+1 round trips and repeated planning/locking work. Fix with a correctly shaped join, batch query using keys, eager/batched loading, or an application-specific aggregate. One giant join can multiply data and memory, so choose a bounded shape and verify latency/load rather than blindly “joining everything.”

9. **[Deep dive] Why do indexes slow writes?**

   **Answer.** Insert/update/delete must maintain each affected index, logging page changes and potentially causing page splits, contention, random I/O, and cache churn. Updating an indexed value is effectively removal plus insertion. Unused/redundant indexes increase storage, backup, maintenance, and optimizer choice without benefit. Monitor usage cautiously over representative periods before removal.

10. **[Design] When is a partial/filtered or expression index useful?**

    **Answer.** A partial index contains only rows satisfying a stable predicate, such as active records, reducing size/write cost for queries that imply that predicate. An expression index stores a computed key such as normalized email. Query expressions/predicates must match the engine's recognition rules, and uniqueness may then apply only to the subset. These are powerful but vendor-specific schema commitments.

11. **[Deep dive] What are database statistics and data skew?**

    **Answer.** Statistics summarize row counts, value distributions, null fractions, distinct counts, correlations, and sometimes multi-column relationships so the optimizer can estimate cardinality. Stale/coarse statistics or highly skewed values make one generic plan poor for some parameters. Update/analyze appropriately, consider extended statistics or query variants, and avoid assuming a plan good for one parameter is universal.

12. **[Design] What does table partitioning solve—and not solve?**

    **Answer.** Partitioning divides one logical table by range/list/hash for pruning, retention, maintenance, placement, or parallelism. It helps only when queries constrain the partition key or operations benefit from partition boundaries; too many partitions increase planning/metadata overhead. Partitioning is not a substitute for indexes, sound schemas, or sharding across independent servers, and global uniqueness/foreign keys may be constrained by the engine.

---

# 4. Transactions and concurrency

1. **[Basic] What does ACID mean in practice?**

   **Answer.** Atomicity commits all transaction effects or none. Consistency means transactions move the database between states satisfying declared/application invariants; the database cannot enforce rules never encoded. Isolation controls how concurrent transactions interact, approximately as if serialized depending on level. Durability means acknowledged commits survive specified failures according to storage/replication configuration. ACID is a set of guarantees with operational scope, not a promise that application logic is correct.

2. **[Basic] What anomalies do isolation levels address?**

   **Answer.** Dirty reads observe uncommitted data; non-repeatable reads see a row change between reads; phantoms change the set matching a predicate; lost updates overwrite concurrent work; write skew violates a cross-row invariant when transactions update different rows based on a shared snapshot. Standard names do not perfectly predict every engine's behavior. Define the anomaly/invariant and verify the selected engine's implementation.

3. **[Deep dive] How does MVCC work conceptually?**

   **Answer.** Multi-Version Concurrency Control retains row versions so readers can use a transaction-consistent snapshot while writers create newer versions, reducing reader/writer blocking. Visibility rules use transaction/version metadata. Old versions need vacuum/garbage collection, and long transactions can retain them and cause bloat. MVCC does not eliminate write conflicts, predicate anomalies, deadlocks, or the need for indexes.

4. **[Deep dive] How do pessimistic and optimistic concurrency control differ?**

   **Answer.** Pessimistic control locks data before conflicting work, appropriate when conflicts are likely/expensive but increasing blocking/deadlock risk. Optimistic control reads a version and performs a conditional update; failure means someone changed it and the operation retries or reports conflict. Optimistic checks must cover the actual invariant and related rows; a last-write-wins update with no version predicate is not optimistic concurrency control.

5. **[Basic] What is a database deadlock?**

   **Answer.** Transactions hold resources and wait in a cycle, so none can progress. The database detects a cycle/timeout and aborts a victim; applications must roll back and may retry the entire idempotent transaction with bounded backoff. Reduce risk with consistent lock/update order, short transactions, suitable indexes, and avoiding user/network waits while holding locks. Eliminating every deadlock is less realistic than making them rare and recoverable.

6. **[Deep dive] Why can a transaction fail at commit time?**

   **Answer.** Deferred constraints, serialization validation, write conflicts, replication/durability failures, disk/full conditions, or connection loss can surface only when committing. Application code must treat commit as fallible and must not publish irreversible external success beforehand. If the connection drops during commit, outcome may be unknown; use idempotency keys/status lookup or a protocol that can reconcile rather than blindly retrying side effects.

7. **[Design] How large should a transaction be?**

   **Answer.** It should be the smallest unit that preserves the business invariant. Long/huge transactions retain versions, locks, log space, and connection capacity and make retries expensive; overly small transactions expose partial state. Batch large migrations with checkpoints and idempotent progress. Never keep a transaction open while waiting for user input or a slow remote service unless a carefully designed protocol requires it.

8. **[Code] How should money transfer be represented transactionally?**

   **Answer.** Lock or conditionally update both accounts in a consistent order, verify source balance/domain rules, write debit, credit, and immutable ledger/audit entries in one transaction, then commit. Use fixed-precision/integer minor units and an idempotency key for retried commands. A transaction only covers one database boundary; external notification follows an outbox/event process after the committed source of truth.

9. **[Deep dive] What is write skew?**

   **Answer.** Two transactions read the same consistent snapshot, each sees an invariant satisfied, and each updates a different row so no direct write conflict occurs; together their commits violate the invariant. Classic example: two on-call doctors each mark themselves off after seeing the other on call. Prevent it with serializable isolation, explicit predicate/advisory locking, or modelling the invariant as a constraint/update on a common row where appropriate.

10. **[Design] What is the transactional outbox pattern?**

    **Answer.** The business update and an outbox message row are committed in one local database transaction. A separate relay publishes outbox rows to a broker and marks progress; it may publish duplicates after crashes, so consumers/message effects must be idempotent. This closes the “database committed but publish failed” gap without distributed two-phase commit, at the cost of relay lag, cleanup, ordering, and deduplication design.

11. **[Deep dive] Why is retrying every transaction error dangerous?**

    **Answer.** Constraint/validation errors are permanent for the request; authentication/schema errors require intervention; timeouts/connection loss may leave outcome unknown; deadlock/serialization failures are usually safe retry candidates only if the whole operation is idempotent. Classify errors using driver/engine codes, impose a total deadline and retry budget with jitter, rebuild transaction state from scratch, and surface exhaustion with context.

12. **[Design] When is distributed two-phase commit appropriate?**

    **Answer.** It can provide atomic commit across participating transactional resources via prepare/commit, but adds coordinator availability, blocking/in-doubt recovery, operational coupling, and limited external-system support. Use it only when atomicity across those resources is essential and infrastructure supports/rehearses recovery. Many service architectures prefer local transactions plus outbox/sagas/idempotent compensation with explicitly weaker intermediate consistency.

---

# 5. C++ integration and database operations

1. **[Basic] Why should prepared statements and parameter binding be used?**

   **Answer.** They keep SQL structure separate from data, preventing SQL injection through quoting mistakes and correctly encoding types/nulls/binary data. They may also reuse parsing/planning, depending on driver/engine. Parameters cannot normally replace identifiers or arbitrary syntax; dynamic table/order choices require an allowlist and safely constructed SQL. Prepared statements do not validate business authorization.

2. **[Deep dive] Which C++ lifetime bugs occur around database bindings and result views?**

   **Answer.** Some APIs copy bound values immediately; others retain pointers until execute/finalize, so temporary `string_view`/buffers can dangle. Result column pointers/views may remain valid only until the next row step, statement reset, or connection action. Read the driver contract, use owning values where data escapes the row scope, and wrap statement/result handles in RAII so exceptions and early returns finalize resources.

3. **[Design] How should a transaction be represented in C++?**

   **Answer.** Use an RAII transaction object that begins explicitly, commits explicitly, and performs non-throwing rollback in its destructor if still active. Commit remains a visible fallible operation; destruction must not pretend success. Prevent accidental copying, define move/thread affinity, and ensure rollback errors are logged/contained without throwing during stack unwinding. The connection must outlive the transaction.

4. **[Basic] Why use a connection pool?**

   **Answer.** Establishing/authenticating connections is expensive and databases have finite connection capacity. A bounded pool reuses connections and applies admission/backpressure. It must validate/reset session state before reuse, enforce acquisition/query deadlines, handle broken connections, and expose utilization/wait metrics. An unbounded pool can overload the database; one connection shared concurrently is often unsupported by the driver.

5. **[Deep dive] What state can leak through a pooled connection?**

   **Answer.** An open/aborted transaction, isolation level, temporary tables, session variables, prepared statements, role/search path, time zone, and advisory locks may affect the next borrower. The pool needs a reliable reset/rollback/discard protocol and should drop connections whose state is uncertain. Application code must return a connection on every path through RAII.

6. **[Design] How should timeouts and cancellation be layered?**

   **Answer.** Set a request deadline, bounded pool-acquisition timeout, statement/query timeout, network/connect timeouts, and transaction budget so inner operations cannot outlive the caller. Cancellation may only request interruption; drain/reset or discard the connection according to the driver protocol before reuse. Distinguish client timeout from confirmed server rollback because the operation may have committed or continue running.

7. **[Deep dive] Blocking database API or asynchronous API?**

   **Answer.** A blocking API is simpler and can scale with a bounded worker pool when concurrency is moderate and threads are acceptable. An async API integrates with event loops and many concurrent waits but complicates lifetime, cancellation, transaction affinity, and backpressure. It does not make the database faster. Choose based on concurrency and runtime architecture, then bound in-flight queries either way.

8. **[Code] How should multiple rows be inserted efficiently?**

   **Answer.** Use a transaction plus prepared batch/multi-row insert or the engine's bulk-copy protocol, respecting parameter/message limits. Validate data and define per-row versus all-or-nothing error semantics. Committing every row multiplies log flush/round-trip cost; one enormous transaction can exhaust log/locks/memory. Benchmark a bounded batch size and preserve idempotent restart checkpoints for imports.

9. **[Design] SQLite or a client-server database?**

   **Answer.** SQLite is an embedded library and single file with simple deployment, excellent local reads, transactions, and limited concurrent writer behavior; it suits device/app state, caches, and moderate embedded workloads. A server database provides network multi-client access, centralized operations, richer replication/permissions/concurrency, and independent scaling at operational cost. Filesystem locking, network filesystems, backup, and process-crash requirements matter; “small data” alone does not decide.

10. **[Deep dive] How should database errors be mapped into application errors?**

    **Answer.** Preserve stable categories/codes such as unique conflict, foreign-key violation, serialization retry, timeout, authentication, unavailable, and unknown outcome, plus safe causal context. Do not branch on localized message text. Translate at the infrastructure boundary into domain outcomes only when semantics are known—for example a named unique constraint can mean “username already exists.” Log once at the final handling boundary without leaking credentials/query secrets.

11. **[Design] How should schema migrations be managed?**

    **Answer.** Keep ordered versioned migrations under review, apply each once with a metadata table/tool, test from supported prior versions and on production-scale copies, and back up/rehearse recovery. Prefer additive expand/migrate/contract changes for zero-downtime overlap. Separate long backfills/index builds from short metadata changes, bound locks/load, and make resumability/observability explicit. Editing an already deployed migration destroys history reproducibility.

12. **[Deep dive] What makes a database backup trustworthy?**

    **Answer.** It is consistent for the engine, includes schema/data and required logs/keys/configuration, is stored separately with access/retention controls, and—most importantly—is regularly restored and validated against recovery objectives. Replication is not backup because deletion/corruption can replicate. Define RPO (acceptable data loss) and RTO (recovery time), preserve point-in-time recovery logs, and test application compatibility after restore.

13. **[Design] What should database observability include?**

    **Answer.** Measure request/query latency and errors by operation (not raw high-cardinality SQL), pool wait/use, active/idle/blocked sessions, locks/deadlocks, connection churn, rows/bytes, slow-query fingerprints, plan changes, storage/log/replication health, and migration progress. Correlate with application traces safely. Do not log sensitive parameters by default; sampling and query normalization control cost/cardinality.

14. **[Deep dive] What happens when a database connection is lost mid-operation?**

    **Answer.** Before commit, the server normally rolls back when it detects disconnect, but the client may not know exactly how far execution went. During/after commit acknowledgement loss, outcome can be unknown. Discard the broken connection, release local state, and reconcile using operation IDs/status queries or retry only idempotent work. Never continue a transaction on a new connection as though it were the same server session.

---

# Assessment usage notes

- Ask candidates to state SQL dialect/engine assumptions when behavior varies.
- A strong query answer includes result semantics before performance; a fast wrong outer join is still wrong.
- For C++ integration, evaluate RAII, view lifetimes, timeouts, pooling, error categories, and unknown commit outcomes.
- Use execution plans and measurements rather than universal “indexes make it fast” rules.
