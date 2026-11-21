---
type: llm
---

Grade the final message.

PASS if it puts the payments rules in a file every listed agent reads, a nested
`services/payments/AGENTS.md`, and says why: `.claude/rules/` is read only by Claude Code, so Codex
and Cursor would never see the rules. Adding a `CLAUDE.md` that links or imports that `AGENTS.md`
is fine.

FAIL if the only place it writes the rules is `.claude/rules/payments.md` or another
single-harness rule file, or if it keeps two copies of the same rules in different files.
