---
description: Replaces a trigger-maintained daily rollup that deadlocks with closed periods computed once, not with ordered or advisory locks.
tags: [postgres-schema-design, skill-fired]
max_turns: 10
allowed_tools: [Read, Glob, Grep, Skill]
---

`daily_usage (account_id, day, events)` is kept current by an `AFTER INSERT` trigger on `events`
that does `INSERT ... ON CONFLICT (account_id, day) DO UPDATE SET events = daily_usage.events + 1`.
Our ingest inserts events in batches that span many accounts, and under load we now get
deadlocks between concurrent batches. The dashboard reads `daily_usage` for the last 90 days.

I was going to sort each batch by account before inserting, or take
`pg_advisory_xact_lock(account_id)` in the trigger. Which one should I do? Postgres 16.

Reply in your message; you don't need to edit files.
