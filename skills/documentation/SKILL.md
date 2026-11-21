---
name: documentation
description: Use when writing or reviewing a README, a docs page, or a code comment, including a diff that adds or changes one. Decide what belongs in docs and comments so they do not drift from the code. For AGENTS.md and skills, also use agent-instructions.
---

# Documentation and comments

Read every applicable `AGENTS.md` first. Repository-local policy overrides these defaults.

Documentation goes stale when it repeats something the code already states. Write down what the
code cannot say, and link to the code for the rest.

## Docs

- Document why a design was chosen, and the invariants nothing enforces. Those are the facts a
  reader cannot recover from the source.
- Do not restate code, configuration values, limits, or counts. They change, and the copy does
  not. Name the setting and point at where it is defined.
- Do not write numbers that change, such as row counts, error rates, latencies, or user counts.
  Write how to get them: the query, command, or dashboard, so the reader fetches the current
  value. A point-in-time document may give a number with its date and source. For how a
  document is structured for its readers, use the `technical-writing` skill.
- Describe the system as it is. Leave out history ("previously", "we used to"), plans, and
  roadmaps; they belong in the tracker and in commit messages.
- In a public repository, leave out private detail: internal hostnames, customer names,
  incident narratives, unreleased plans.
- When a change alters behavior a document describes, update the document in the same change.

## Comments

- Comment only a local invariant the code cannot show: why this order matters, what a caller must
  hold, which unusual input this branch exists for.
- Do not write a comment that explains the reason for a design or the history of a change. Put
  the design reason in the docs and the history in the commit message.
- Do not narrate what the next line does.
- Delete a comment when the code it describes goes.

## Agent instruction files

For what goes in `AGENTS.md`, a skill, or a docs page, and how they link, use the
`agent-instructions` skill.
