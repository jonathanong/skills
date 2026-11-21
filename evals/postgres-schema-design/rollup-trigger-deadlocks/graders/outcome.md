---
type: llm
---

Grade the final message.

PASS if the recommendation is to stop maintaining `daily_usage` from the trigger, and instead to
compute each closed day once (a scheduled job, or a materialized view refreshed on a schedule)
and to read the current day from `events` directly or from a cheap query over today's rows.

FAIL if the recommended fix keeps the per-row trigger and adds lock ordering, sorted batches, an
advisory lock, or a retry on deadlock to make it work. Mentioning these only to reject them is
not a failure.
