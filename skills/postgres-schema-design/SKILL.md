---
name: postgres-schema-design
description: Use when writing or reviewing a migration, a table, column, or foreign key, or any code that takes a row lock (FOR UPDATE) or an advisory lock, serializes concurrent writers, or enforces a cap, limit, or last-one guard under concurrency. Designs PostgreSQL tables, names, relations, history, work-item and cursor tables, retention, and locking.
---

# PostgreSQL schema design

Read every applicable `AGENTS.md` first. Repository-local policy overrides these defaults.

- Names explain the stored thing; read [naming](references/naming.md) when naming schema objects.
- Columns say their type and FK target; finite sets use enums or lookup tables.
- Joined identities use foreign keys; read [relations](references/relations.md) before modeling references.
- Split differing shapes, share identical values, and keep history documents as JSON.
- Use canonical [table shapes](references/table-shapes.md) for revisions, changes, work, and cursors.
- Default to finite retention for everything except user content; see [table shapes](references/table-shapes.md#retention).
- Take no unnecessary locks; read [Locks](#locks) before adding `FOR UPDATE`, a counter, or a claim.
- Reuse parameterized trigger functions instead of copying bodies.
- Comment every table, column, and view; document reviewed exceptions explicitly.
- Name every constraint and state `ON DELETE` on every foreign key. Index the referencing column.
- Keys are generated ids. A natural text key stays a unique column on its owning table; other
  tables reference the id. See [relations](references/relations.md).

## Locks

Most writes need no lock: a comment, a like, a follow, a profile edit. Each lock adds waiting,
deadlock risk, and a reason for the next reader to keep it, and one held too long stalls every
writer queued behind it. Start with none. When more than one mechanism is correct, pick the
simplest.

- Lock for an exact quantity: money, a balance, a ledger, inventory, anything that must never
  drift. Even there, prefer a conditional write
  (`UPDATE stock SET qty = qty - $1 WHERE sku = $2 AND qty >= $1`) or a constraint. When the rule
  spans rows those cannot express, a row lock (`FOR UPDATE`) on the rows the transaction writes,
  or on their parent, is acceptable.
- Also lock for a rare race on a privacy, legal, or irreversible invariant: an account-deletion
  fence, a legal hold, a delete in an outside store that cannot be undone. Lock the aggregate's
  root row (`SELECT ... FROM users WHERE id = $1 FOR UPDATE`, or `FOR SHARE` for the side that
  only needs it to stay put), even when the transaction does not write that row. Take an advisory
  lock only when no row exists to lock, or when the critical section must span transactions or
  outside I/O; then hold it on a dedicated connection and release it in a `finally`.
  A cap or a "don't remove the last X" guard is not such an invariant, even when it protects
  access; it stays best effort, below.
- Anywhere else, never lock a read set or a parent row to serialize writers.
- Except for that advisory case, every lock is transaction-scoped and short, and:
  - waits a bounded time: `NOWAIT` or `lock_timeout` in a request path, returning 409;
  - takes rows in a fixed order, parent before child, so two writers cannot deadlock;
  - covers no outside call. An enqueue is an outside call: commit, then enqueue. A slow queue
    store inside the transaction stretches every lock it holds.
- Otherwise avoid advisory locks. Prefer a constraint, a conditional write, or `SERIALIZABLE`, below. When a
  change does take one, it says why those do not fit, and a review checks the lock itself: it is
  transaction-scoped, its wait is bounded (`pg_try_advisory_xact_lock`, or `lock_timeout`; the
  blocking form holds a pooled connection while it waits), and its key covers exactly the writers
  that conflict. A key such as `hashtext(org_id)` alone is shared by every other lock keyed on the
  same id; use the two-key form with a namespace for the operation.
- A cap or a "don't remove the last X" guard is best effort. Count up to the cap
  (`LIMIT cap + 1`), then write. Two concurrent requests may land one row over; say so and accept
  it. When asked to make a cap exact, answer with this design. Do not add a lock, a counter or
  projection table, a slot or claim table, or a constraint that exists only to make the cap exact.
- An invariant whose violation corrupts data (a unique name, a balance that cannot go negative,
  one active row per parent) is closed by the narrowest of: a constraint, a conditional write
  (`UPDATE ... WHERE <condition>`, `INSERT ... ON CONFLICT`), or, for a rare race across rows, a
  `SERIALIZABLE` transaction whose `40001` is returned as 409 with no retry. A scheduled job leaves
  the work to its next run. Outside the two lock cases above, never close a race with a wider lock.
- Do not add generation, revision, claim, or counter columns to coordinate concurrent writers. Make
  the write idempotent or use the options above. Two exceptions: an exact quantity, and an
  optimistic version column on an entity people edit by hand, so a lost update returns 409. A
  lease token on leased work is not a coordination column.
- Do not keep a counter or rollup table current with triggers when that needs global or ordered
  locks to avoid deadlocks. Compute each closed period once, with a scheduled job or a
  materialized view, and read the open period from the source rows.
- Remove a lock by deleting it, not by swapping in another serializer.

For query shape, read [performance patterns](../postgres-node-performance-tuning/references/performance-patterns.md).
For partition lifecycle, use [UUIDv7 partitioning](../postgres-partitioning-uuid-v7/SKILL.md).
For how jobs choose, cap, and resume over work-item and cursor tables, read
[bounded iteration](../bounded-iteration/SKILL.md).
A consuming repository owns lifecycle, retention windows, migration strategy, and enforcement
configuration.
