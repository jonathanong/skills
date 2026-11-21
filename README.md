# Harness plugins

The `harness-plugins` marketplace of agent plugins for Claude Code and Codex. Add the marketplace
once, then install any plugin from it.

## Plugins

| Plugin                                                                                              | What it does                                                                             |
| --------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| [jong-skills](skills)                                                                               | Generic skills that explain a design or a procedure; listed [below](#jong-skills).       |
| [no-mistakes](https://github.com/jonathanong/no-mistakes)                                           | Answers how code connects from the import graph: what uses a symbol, which tests to run. |
| [no-mistakes-rules](https://github.com/jonathanong/no-mistakes/tree/main/plugins/no-mistakes-rules) | MUST NOT / SHOULD NOT rule skills that `no-mistakes` checks against.                     |
| [pr-shepherd](https://github.com/jonathanong/pr-shepherd)                                           | Takes a pull request through CI, review comments, and merge.                             |
| [service-status](https://github.com/jonathanong/service-status)                                     | Tells an agent when a failure is a third-party outage and not its bug.                   |
| [hook-harness](https://github.com/jonathanong/hook-harness)                                         | Turns GitHub events into agent runs.                                                     |
| [agent-blackboard](https://github.com/jonathanong/agent-blackboard)                                 | Records agent telemetry so agents can improve from their own sessions.                   |
| [code-reviewer](https://github.com/jonathanong/code-reviewer)                                       | Standing multi-pillar code reviewer with label intake and follow-up reviews.             |

`jong-skills` lives in this repository. Each other plugin lives in its own repository, and the
marketplace points at it.

## Install

Claude Code:

```bash
claude plugin marketplace add jonathanong/harness-plugins
claude plugin install jong-skills@harness-plugins
claude plugin install pr-shepherd@harness-plugins
```

Codex:

```bash
codex plugin marketplace add jonathanong/harness-plugins --ref main
codex plugin add jong-skills@harness-plugins
codex plugin add pr-shepherd@harness-plugins
```

A plugin whose manifest sets no `version`, such as `jong-skills`, has no releases to track: Claude
Code treats each commit on its `main` as an update. A plugin whose manifest sets a `version`
updates only when its repository releases a new version. Codex picks up changes when you run
`codex plugin marketplace upgrade harness-plugins`.

### Moving from a plugin's own marketplace

If you added one of these repositories as its own marketplace, remove that marketplace and
reinstall every plugin you used from it from `harness-plugins`. The no-mistakes marketplace holds
both `no-mistakes` and `no-mistakes-rules`. Claude Code uninstalls a marketplace's plugins when you
remove it. Codex leaves them enabled, so uninstall them first or you end up with two copies. For
pr-shepherd:

```bash
claude plugin marketplace remove pr-shepherd
claude plugin install pr-shepherd@harness-plugins

codex plugin remove pr-shepherd@pr-shepherd
codex plugin marketplace remove pr-shepherd
codex plugin add pr-shepherd@harness-plugins
```

## jong-skills

Generic agent skills that explain a design or a procedure. A skill here may say which approach to
pick and why. It holds no organization-specific policy; a consuming repository adds its own rules
on top, in its `AGENTS.md`. Skills load as `jong-skills:<skill>`, for example
`jong-skills:dependabot`.

| Skill                                                                                  | Use it when                                                                    |
| -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| [`static-code-analysis`](skills/static-code-analysis/SKILL.md)                         | Creating a repository: which static checks to add and how strict to start.     |
| [`static-analysis-checklist`](skills/static-analysis-checklist/SKILL.md)               | Adding or changing one static-analysis invariant.                              |
| [`github-actions-authoring`](skills/github-actions-authoring/SKILL.md)                 | Writing workflows: events not polling, triggers, timeouts, runners, pins.      |
| [`dependabot`](skills/dependabot/SKILL.md)                                             | Configuring Dependabot coverage, grouping, cooldowns, secrets, and runs.       |
| [`github-repository-settings`](skills/github-repository-settings/SKILL.md)             | Limiting pull requests to collaborators and gating outside contributors' CI.   |
| [`package-json-checklist`](skills/package-json-checklist/SKILL.md)                     | Changing `package.json`, a lockfile, or a package entry point.                 |
| [`npm-publishing`](skills/npm-publishing/SKILL.md)                                     | Preparing a first npm publish, trusted publishing, and the release workflow.   |
| [`triage-codex-security`](skills/triage-codex-security/SKILL.md)                       | Triaging Codex Security findings into explicitly approved actions.             |
| [`postgres-schema-design`](skills/postgres-schema-design/SKILL.md)                     | Designing tables, relations, history, retention, and row locking.              |
| [`postgres-node-performance-tuning`](skills/postgres-node-performance-tuning/SKILL.md) | Writing or tuning a query: shape, indexes, plans, round trips.                 |
| [`postgres-partitioning-uuid-v7`](skills/postgres-partitioning-uuid-v7/SKILL.md)       | Partitioning and pruning a table keyed by UUIDv7.                              |
| [`bounded-iteration`](skills/bounded-iteration/SKILL.md)                               | Writing a sweep, backfill, dispatcher, or list read that must stay bounded.    |
| [`glide-mq`](skills/glide-mq/SKILL.md)                                                 | Designing glide-mq queues, lanes, concurrency, rate limits, and leases.        |
| [`evals`](skills/evals/SKILL.md)                                                       | Choosing kinds of eval, and documenting scores, run history, and rules.        |
| [`pr-description`](skills/pr-description/SKILL.md)                                     | Writing a pull request description: problem, change, impact, before/after.     |
| [`documentation`](skills/documentation/SKILL.md)                                       | Deciding what goes in docs and code comments.                                  |
| [`agent-instructions`](skills/agent-instructions/SKILL.md)                             | Deciding what goes in `AGENTS.md`, a skill, docs, or a check.                  |
| [`technical-writing`](skills/technical-writing/SKILL.md)                               | Structuring a document for scanning: summary first, audiences, facts, terms.   |
| [`test-authoring`](skills/test-authoring/SKILL.md)                                     | Which tests to write; fixtures, mocks, guards; Vitest, Storybook, Swift, .NET. |
| [`playwright-authoring`](skills/playwright-authoring/SKILL.md)                         | Writing browser tests: what needs a browser, locators, and test ids.           |
| [`typescript-authoring`](skills/typescript-authoring/SKILL.md)                         | Writing TypeScript without aliases, shims, or hidden calls.                    |
| [`react-app-structure`](skills/react-app-structure/SKILL.md)                           | Structuring API calls, links, redirects, and config in a React app.            |
| [`native-app-architecture`](skills/native-app-architecture/SKILL.md)                   | Structuring a native app around a portable core with thin views.               |
| [`rust-authoring`](skills/rust-authoring/SKILL.md)                                     | Laying out Rust tests and caches, and running heavy cargo builds.              |
| [`http-api-design`](skills/http-api-design/SKILL.md)                                   | Shaping an HTTP API: resources, thin handlers, identifiers, secrets.           |
| [`service-boundaries`](skills/service-boundaries/SKILL.md)                             | Calling another service or a partner API, or handling a webhook.               |
| [`feature-rollout`](skills/feature-rollout/SKILL.md)                                   | Adding a feature flag, or a table or service that needs an entry point.        |
| [`structured-logging`](skills/structured-logging/SKILL.md)                             | Writing log lines with a static message and structured fields.                 |
| [`agent-workflow`](skills/agent-workflow/SKILL.md)                                     | Implementing, handing off, reviewing, and answering review comments.           |
| [`planning`](skills/planning/SKILL.md)                                                 | Planning a change: options, what it touches, steps, where the plan lives.      |
| [`git-commit-checklist`](skills/git-commit-checklist/SKILL.md)                         | Committing, and rebasing or force-pushing a branch others also push to.        |
| [`github-issue`](skills/github-issue/SKILL.md)                                         | Filing, editing, closing, and linking GitHub issues.                           |
| [`review-ci-logs`](skills/review-ci-logs/SKILL.md)                                     | Diagnosing a CI failure or noisy CI output from the logs.                      |
| [`revisit-followups`](skills/revisit-followups/SKILL.md)                               | Sweeping merged work for deferred follow-ups that are still needed.            |

### Moving from `skills@jonathanong-skills`

This plugin was `skills@jonathanong-skills`, from the repository `jonathanong/skills`. Remove the
old plugin and marketplace, then install as above:

```bash
claude plugin uninstall skills@jonathanong-skills
claude plugin marketplace remove jonathanong-skills
codex plugin remove skills@jonathanong-skills
codex plugin marketplace remove jonathanong-skills
```

Skills now load as `jong-skills:<skill>`, not `skills:<skill>`, so update any instruction file or
setting that names a skill with the old prefix.

### Layout

```
.claude-plugin/     Claude Code plugin and marketplace manifests
.codex-plugin/      Codex plugin manifest
.agents/plugins/    Codex marketplace manifest
plugin.json         Open plugin manifest
skills/<skill>/     SKILL.md and its references
evals/<skill>/      Eval cases for that skill, one directory per case
evals/README.md     Current eval scores and what each case checks
AGENTS.md           Rules for contributors and agents, including the skill size limit
```

To add a skill, create `skills/<skill>/SKILL.md` and at least one case under `evals/<skill>/` that
asserts the skill fired. Keep `SKILL.md` to 150 lines and put detail in `references/`.
`pnpm run evals:check` enforces both, and CI runs it. See [`AGENTS.md`](AGENTS.md).

### Evals

Every skill has eval cases run with [`claude plugin eval`](https://code.claude.com/docs/en/plugin-evals).
The current scores and what each case checks are in [`evals/README.md`](evals/README.md). The run
log is in [`evals/HISTORY.md`](evals/HISTORY.md), and the rules for cases are in
[`evals/AGENTS.md`](evals/AGENTS.md).

```bash
pnpm run evals                  # every skill
pnpm run evals npm-publishing   # one skill
```

### Development

```bash
pnpm install
pnpm run lint
pnpm run format:check
pnpm run evals:check
pnpm run marketplace:check
```

## License

[MIT](LICENSE)
