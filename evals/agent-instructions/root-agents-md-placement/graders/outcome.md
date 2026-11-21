---
type: llm
---

Grade the final message.

PASS if both hold: it moves the release procedure out of the root `AGENTS.md` into something
loaded only when releasing (a skill, or a release doc that `AGENTS.md` links with one line),
because the root file is loaded for every task; and it removes the billing file-and-exports table,
or replaces it with a pointer to `src/billing/`, because the code already shows it and the copy
will drift.

FAIL if the tidied `AGENTS.md` still contains the full release steps or the file-and-exports
table, or if it adds more reference material of the same kind to the root file.
