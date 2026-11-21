---
name: agent-instructions
description: Use when writing, reviewing, or setting up rules or instructions for coding agents - AGENTS.md, CLAUDE.md, path-scoped rules such as .claude/rules or Cursor rules, skills, or docs for agents - or deciding where a rule, procedure, or explanation should live. Puts principles in AGENTS.md, procedures in skills, explanations in linked docs, and checkable rules in checks.
---

# Agent instructions

Read every applicable `AGENTS.md` first. Repository-local policy overrides these defaults.

Every line an agent loads costs context on every task that loads it. Put each instruction where it
is loaded only when it is needed, and nowhere else.

## Where each thing goes

| What                                                    | Where                         | Loaded                              |
| ------------------------------------------------------- | ----------------------------- | ----------------------------------- |
| A principle that holds for all work in a directory      | `AGENTS.md` in that directory | Always, for work there              |
| A procedure or set of rules one kind of task needs      | A skill                       | On demand, when its trigger fits    |
| How a system works, why it is built so, and its gotchas | A docs page                   | When a link or a person leads to it |
| A rule a machine can check                              | A lint rule or CI check       | On every change, without fail       |
| What the code, schema, or configuration already shows   | Nowhere                       | Read from the source                |

- **`AGENTS.md`** holds short, imperative principles. Give it a size budget and hold to it. Move
  anything only some tasks need into a skill, and anything that explains into docs.
- **A skill** holds what one kind of work needs: steps, defaults, and the reasons. Open its
  description with when to use it ("Use when writing or reviewing ..."); an agent loads a skill
  far more often when the trigger comes first. Keep the body short and move long detail into
  reference files the body links with one line each.
- **Docs** are for people first: a wiki that explains how a system works. Lead with tables and
  Mermaid diagrams, and spend the prose on why a design was chosen and on gotchas a reader would
  not guess. For what belongs in a document, use the `documentation` skill; for how to structure
  one, use `technical-writing`.
- **Do not document what the code shows.** File lists, function signatures, configuration values,
  and dependency lists drift the day they are copied. Name the file or setting and let the reader
  open it.
- **Enforce what you can.** A rule in prose is advice an agent may miss. When a pattern can be
  detected, add the check (see `static-analysis-checklist`) and keep at most one line in
  `AGENTS.md` that names it.

## Scope and loading

- Scope a rule to the directory it governs with a nested `AGENTS.md`. A nested file adds to its
  parent and never repeats it.
- Write instructions in portable files: `AGENTS.md`, skills, and docs. Do not put rules in one
  harness's own rule format, such as Claude Code's `.claude/rules/`; another agent never sees
  them. Use a nested `AGENTS.md` for a path-scoped rule.
- When a harness reads only its own file name, such as `CLAUDE.md`, make that file a symlink to
  `AGENTS.md` or a one-line import of it. Never keep two copies.
- Keep generic skills in a shared plugin and a repository's own skills in that repository. A
  rule that holds only for one repository does not belong in a shared skill.

## Links

- Link each docs page from the `AGENTS.md` or skill of the area it explains, with one line that
  says when to read it. An unlinked page is never read by an agent.
- Check links in CI, for example with `lychee`, so a moved or deleted page fails the build
  instead of leaving a dead pointer.
- Link a document instead of copying it. State each rule once and link to it from elsewhere.

## Capture decisions

- When a decision you reach with a person will come up again, ask whether to encode it, and
  where: `AGENTS.md`, a skill, docs, or a check.
- Do not keep a principle, or a repository or global rule, only in an agent's memory. Memory
  belongs to one agent on one machine; other agents, other people, and CI never see it. Write the
  rule where it is shared, and keep memory for personal preferences and pointers.

## Evals

- Give a skill eval cases when its behavior matters: one that checks the skill loads for the plain
  requests that should trigger it, and one that checks the answer follows its rules. Do the same
  for an agent definition or an instruction file when a runner can test it. For how to run and
  record them, use the `evals` skill.
- Change a description or rule only on evidence from those cases, measured against the old version
  in the same session.

## Writing a rule

- Write what to do, with the reason when it is not obvious. A reader who knows the reason can
  handle the case the rule did not foresee.
- Write the rule, not the incident that produced it. The story belongs in the commit or the
  issue.
- Prefer one short example to a paragraph of description.
- State the principle, or how to do the thing, not a number that drifts. Name where a limit or
  setting is defined, or how to look it up, instead of copying its value.
- Leave out private detail, as in any document.

## Review

Ask of a change to agent instructions:

- Is anything in `AGENTS.md` needed only by some tasks? Move it to a skill.
- Does any file restate what the code shows? Delete it and point at the source.
- Could a check enforce this rule instead of prose?
- Is any rule in a harness-specific file, or copied into two files?
- Is every new docs page linked from where an agent will look, and is the link checked?
- Does any rule hold a number that will drift?
- Was a decision from this work saved only to memory?
- Does a new or changed skill have eval cases for loading and for its rules?
