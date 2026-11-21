---
description: Scopes rules to a directory with a nested AGENTS.md, not Claude Code's `.claude/rules/`, so every agent sees them.
tags: [agent-instructions, skill-fired]
max_turns: 10
allowed_tools: [Read, Glob, Grep, Skill]
---

Our repo is used by Claude Code, Codex, and Cursor agents. I want a few rules that only apply when
someone edits code under `services/payments/`: amounts are integer cents, every external call
takes an idempotency key, and no retries on a 4xx. I was going to add
`.claude/rules/payments.md` with a `paths: services/payments/**` frontmatter. Write the file(s)
for me.

Reply in your message with the file paths and contents; you don't need to create files.
