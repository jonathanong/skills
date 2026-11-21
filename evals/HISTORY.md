# Eval history

This log only grows, newest entry first. Scores in different entries are not comparable when a
case, grader, fixture, or model changed between them; each entry says what changed. The current
scores are in [README.md](README.md).

## 2026-11-21: locks for rare privacy, legal, and irreversible invariants

Commit after `bd25591`, Sonnet agent, Opus judge, Claude Code 2.1.330, $7.51 in total.

- **Rule change:** `postgres-schema-design` now allows a lock for a rare race on a privacy,
  legal, or irreversible invariant (an account-deletion fence, a legal hold, a delete in an
  outside store): a `FOR SHARE` or `FOR UPDATE` on the aggregate's root row even when it is not
  written, and an advisory lock only when no row exists or the section spans transactions or
  outside I/O. The simplest correct mechanism wins. A trigger-maintained counter or rollup that
  needs global or ordered locks is replaced by closed periods computed once. The default (no
  lock, constraint or conditional write, `SERIALIZABLE` and 409 for rare races) is unchanged.
- **Cases:** added `rollup-trigger-deadlocks`. `deletion-fence-root-row-lock` was dropped: it
  scored 1.00 without the plugin in three attempts, and no case for the exception fails without
  it, so it could not stand as a guard.
- **Two-arm run,** three attempts per arm:

| Case                                                     | With | Passed | Without |
| -------------------------------------------------------- | ---- | ------ | ------- |
| `postgres-schema-design/checkout-exact-stock`            | 1.00 | 3 of 3 | 0.67    |
| `postgres-schema-design/deletion-fence-root-row-lock`    | 1.00 | 3 of 3 | 1.00    |
| `postgres-schema-design/exact-cap-no-lock`               | 1.00 | 3 of 3 | 0.00    |
| `postgres-schema-design/review-advisory-lock-key`        | 0.67 | 1 of 3 | 1.00    |
| `postgres-schema-design/review-last-admin-advisory-lock` | 0.50 | 1 of 3 | 0.00    |
| `postgres-schema-design/rollup-trigger-deadlocks`        | 1.00 | 3 of 3 | 0.00    |

- **Last-admin regression:** a six-attempt rerun with traces passed 4 of 6. Both failures
  recommended deleting the lock; one added that `FOR UPDATE` on the organization row would fit if
  the guard had to be exact, which the new root-row wording invites. The exception now says a cap
  or a "don't remove the last X" guard is not such an invariant. A second six-attempt rerun passed
  6 of 6.
- **`review-advisory-lock-key`** missed only on loading (two attempts called no skill), as before.

## 2026-11-21: review fixes to the new cases, and `packageExtensions` corrected

Commit after `3bc6ef4`, Sonnet agent, Opus judge, Claude Code 2.1.330, both arms, three attempts,
$4.83. A review of the previous entry's new cases found graders that also required rules from
other parts of their skill, and one wrong rule in a skill.

- **Skill correction.** `package-json-checklist` said to correct a package's wrong metadata with
  `packageExtensions`. pnpm spreads the extension under the package's own manifest, so it can only
  add what the manifest lacks and cannot change a declared peer range. The rule now uses
  `packageExtensions` for a missing dependency or peer, `peerDependencyRules.allowedVersions`
  scoped to the package for a peer range that is too narrow, and a patch for anything else. The
  description also names workspace metadata. Earlier `review-override-for-peer-warning` scores
  graded the wrong advice as correct; they are not comparable with this entry.
- **Graders narrowed** to the rule each case is named for: `github-issue/stale-notes-before-filing`
  (no issue-body requirement), `revisit-followups/plan-issue-unchecked-boxes` (no separate
  verification step), `rust-authoring/parallel-cargo-jobs` (no CI or `target/` requirement),
  `structured-logging/review-lookup-in-catch` (no fixed-message requirement).
- **Graders corrected:** `native-app-architecture/generated-project-file` no longer accepts a
  local Swift package as the project specification; `playwright-authoring/review-computed-test-ids`
  narrows the row by the invoice number it shows, not the id.

| Case                                                      | With | Passed | Without |
| --------------------------------------------------------- | ---- | ------ | ------- |
| `github-issue/stale-notes-before-filing`                  | 1.00 | 3 of 3 | 0.33    |
| `native-app-architecture/generated-project-file`          | 1.00 | 3 of 3 | 0.00    |
| `package-json-checklist/review-override-for-peer-warning` | 1.00 | 3 of 3 | 0.67    |
| `playwright-authoring/review-computed-test-ids`           | 1.00 | 3 of 3 | 0.00    |
| `revisit-followups/plan-issue-unchecked-boxes` (dropped)  | 1.00 | 3 of 3 | 1.00    |
| `rust-authoring/parallel-cargo-jobs` (dropped)            | 1.00 | 3 of 3 | 1.00    |
| `structured-logging/review-lookup-in-catch`               | 1.00 | 3 of 3 | 0.00    |

The two dropped cases failed without the plugin only on the extra rules; narrowed to their own
rule they do not discriminate. `revisit-followups` and `rust-authoring` have one case each again,
so the previous entry added 10 cases that stay, not 12.

## 2026-11-21: descriptions open with when to use them, and 12 more cases

Commit `9f4aaf5` (runs on its working tree), Sonnet agent, Opus judge, Claude Code 2.1.330.
About $60 across every run below, plus the one-attempt pilots made while writing the cases.
The description comparison ran both arms in the same session, so neither side is a stored number.

**Why.** A rerun of the five `test-authoring` cases on 2.1.330, with no change, loaded the skill in
10 of 15 attempts; on 2026-11-20 (2.1.329) the two review cases had loaded it every time. Two
attempts each of `postgres-schema-design`, `typescript-authoring`, and `glide-mq` missed 3 of 22.
In every miss the agent called no skill. Most descriptions opened with what the skill does and put
"Use when" second.

**Description order, measured.** The three cases that missed most, eight attempts each, with
main's descriptions and with `test-authoring` and `postgres-schema-design` reordered to open with
"Use when" ($5.97 in all):

| Case                                                | Old    | Trigger first |
| --------------------------------------------------- | ------ | ------------- |
| `test-authoring/dotnet-private-field-not-reflected` | 3 of 8 | 6 of 8        |
| `test-authoring/review-helper-holds-assertions`     | 5 of 8 | 8 of 8        |
| `postgres-schema-design/review-advisory-lock-key`   | 0 of 8 | 3 of 8        |

Every skill's description now opens with when to use it. `test-authoring` also names xUnit and
NUnit and a review of a diff that adds tests; `typescript-authoring` names a review of a diff;
`package-json-checklist` had no "Use when" and now has one; `http-api-design` names an edge or
CDN worker in front of an API.

**Rule change.** `typescript-authoring` now says a review asks for the callers to be updated and
does not offer keeping an alias as a fallback. Two `rename-without-alias` answers argued against
the alias and then ended "if you still want the alias, keep it".

**New cases.** A second case for 12 skills that had one: `documentation`, `github-issue`,
`github-repository-settings`, `native-app-architecture`, `package-json-checklist`,
`playwright-authoring`, `pr-description`, `revisit-followups`, `rust-authoring`,
`service-boundaries`, `static-analysis-checklist`, and `structured-logging`. Each was piloted with
one attempt per arm and failed without the plugin.

- **Dropped:** `planning/stale-issue-paths` failed its answer on every attempt, because its grader
  also required two rules from other steps of the skill. Narrowed to the stale-paths rule, it
  scored 1.00 without the plugin. `review-ci-logs/passes-locally-fails-in-ci` scored 1.00 without
  the plugin over three attempts. Neither discriminates.
- **No case found** for `agent-workflow`: three designs either passed without the plugin or
  failed with it. `agent-workflow`, `planning`, and `review-ci-logs` still have one case each.
- **After the runs:** the run URL in `pr-description/ci-gap-section` was put in backticks so the
  link check skips it. The prompt is otherwise unchanged and was not re-run.
- **Rules from this entry:** the root `AGENTS.md` now says a description opens with when to use
  it. `evals/AGENTS.md` adds how to measure a skill that does not load (eight attempts per arm,
  kept traces), that a grader checks only the rule its case is named for, and that a case may
  check a companion skill loaded but never that a neighbor did not.
- **Companion skills:** a `playwright-authoring` pilot checked that `test-authoring` also loaded;
  it did not, and the grader was removed since the review touched no general test rule.

**Full run** (78 cases, 234 attempts, $35.50). The skill loaded in 224 of 234 attempts. Misses:
`documentation/write-scale-numbers` 0 of 3 (2 of 3 on a rerun that kept traces),
`http-api-design/edge-claims-do-not-authorize` 0 of 3, `bounded-iteration/review-dispatcher-predicate-and-job-id`
1 of 3, `postgres-schema-design/review-advisory-lock-key` 1 of 3. The kept traces show the agent
answering with no tool call.

**Two-arm rerun** after naming edge workers in `http-api-design`, three attempts per arm:

| Case                                                  | With | Passed | Without |
| ----------------------------------------------------- | ---- | ------ | ------- |
| `http-api-design/edge-claims-do-not-authorize`        | 1.00 | 3 of 3 | 0.00    |
| `service-boundaries/topology-in-app-repo`             | 0.33 | 1 of 3 | 0.00    |
| `planning/stale-issue-paths` (narrowed, dropped)      | 0.67 | 1 of 3 | 1.00    |
| `review-ci-logs/passes-locally-fails-in-ci` (dropped) | 1.00 | 3 of 3 | 1.00    |

`service-boundaries/topology-in-app-repo` loaded the skill on 1 of 3 attempts here and 3 of 3 in
the full run; both misses failed the answer.

## 2026-11-20: new `technical-writing` skill, and no changing numbers in living docs

Sonnet agent, Opus judge, Claude Code 2.1.329, both arms, three attempts, $3.91, after a
one-attempt pilot ($0.83) in which all three new cases passed with the plugin and failed without.
The new `technical-writing` skill orders a document from summary to evidence, labels sections by
audience and intent, prefers tables and Mermaid diagrams, labels unverified facts and states
assumptions, and defines terms at first use. `documentation` now says not to write changing
numbers into a living document, but how to fetch them.

| Case                                            | With | Without |
| ----------------------------------------------- | ---- | ------- |
| `documentation/review-readme-drift`             | 1.00 | 0.67    |
| `technical-writing/incident-writeup-from-notes` | 0.83 | 0.00    |
| `technical-writing/proposal-for-two-audiences`  | 0.83 | 0.00    |
| `technical-writing/review-runbook-wall-of-text` | 1.00 | 0.00    |

- **New cases:** all three fail every attempt without the plugin.
- **Misses with the plugin were in the answer.** The skill loaded on every attempt. One incident
  write-up labeled sections for everyone and engineering but gave the support lead no section of
  their own. One proposal had a flag-by-plan table but no before and after table.
- **`documentation/review-readme-drift`** scored 0.67 without the plugin, against 0.00 when it
  was added.

## 2026-11-20: locks only for exact quantities, enqueue after commit, database fixtures

Sonnet agent, Opus judge, Claude Code 2.1.329, both arms, three attempts. `postgres-schema-design`
now allows a row or parent lock only for an exact quantity (money, balances, inventory), requires
every lock to be short, bounded, ordered, and free of outside calls including an enqueue, asks for
the try variant or `lock_timeout` on an advisory lock, and allows a version column for exact
quantities and hand-edited entities. `test-authoring` creates database rows through production
factories, and inserts directly only for a state production can no longer create.

Two runs. The `test-authoring` rows are from a $7.52 run of both skills. The
`postgres-schema-design` rows are from a $3.38 run after its new case was rewritten.

| Case                                                     | With | Without |
| -------------------------------------------------------- | ---- | ------- |
| `postgres-schema-design/checkout-exact-stock` (guard)    | 1.00 | 1.00    |
| `postgres-schema-design/exact-cap-no-lock`               | 1.00 | 0.00    |
| `postgres-schema-design/review-advisory-lock-key`        | 0.83 | 1.00    |
| `postgres-schema-design/review-last-admin-advisory-lock` | 1.00 | 0.00    |
| `test-authoring/dotnet-private-field-not-reflected`      | 0.50 | 0.33    |
| `test-authoring/fake-routes-by-request`                  | 1.00 | 1.00    |
| `test-authoring/fixture-files-not-inline`                | 1.00 | 0.00    |
| `test-authoring/review-direct-row-inserts`               | 1.00 | 0.67    |
| `test-authoring/review-helper-holds-assertions`          | 1.00 | 0.00    |

- **New guard `checkout-exact-stock`:** checkout that must never oversell keeps stock exact
  without an unbounded lock and enqueues after commit. It first ran as `review-stock-lock-enqueue`,
  a review of a stock lock added above two unchanged queue calls: 0.67 with the plugin (the skill
  did not load on two attempts) and 1.00 without, so the model already moves an enqueue out of a
  locked transaction in review. Rewritten as a write request, a one-attempt pilot ($1.05) also
  passed without the plugin; the model writes a conditional decrement and enqueues after commit
  on its own. It is kept as the guard beside `exact-cap-no-lock`: the "avoid locks" rule must not
  stop protection of an exact quantity.
- **New case `review-direct-row-inserts`:** passes 3 of 3 with the plugin and 0.67 without, a
  weak gap.
- **`fake-routes-by-request` scored 1.00 without the plugin,** against 0.00 when it was added.
  The model now passes it on its own.
- **Below 1.00:** every miss was the skill not loading, except one
  `dotnet-private-field-not-reflected` attempt whose answer also failed.
- **Fix:** `github-actions-authoring/no-actions-write-with-checked-out-code` had an unquoted
  description containing `: `, which YAML rejects, so a run that included it stopped loading
  cases. The description is now quoted. That case is still unmeasured.

## 2026-11-19: six new rules and cases, not run

No run. Six new cases, one for each new rule: an index change rechecks every dependent query
(`postgres-node-performance-tuning`), a terminal recovery path for permanently failed jobs
(`glide-mq`), no `actions: write` for a job that runs checked-out code
(`github-actions-authoring`), edge-verified claims never authorize (`http-api-design`), `UNLINK`
in one batched call (`typescript-authoring`), and aborting superseded requests
(`react-app-structure`). The cases and their graders are unmeasured: no check was made that the
model fails them without the skill, and none that the skill loads. Their `README.md` rows read
`n/a`.

## 2026-11-17: batch-first I/O and a queue of its own for transactional jobs

Sonnet agent, Opus judge, Claude Code 2.1.327. Two new cases and two rules: per-item I/O takes a
list and the single-item form wraps it (`typescript-authoring`), and a job that commits with or is
triggered by a database transaction gets its own queue (`glide-mq`). Both arms, three attempts.
The `glide-mq` rows are from a $4.67 run of every `glide-mq` and `typescript-authoring` case. The
`typescript-authoring` rows are from the last of five runs of that skill, $1.41 to $2.10 each,
made while rewriting the new case.

| Case                                        | With | Without |
| ------------------------------------------- | ---- | ------- |
| `glide-mq/outbox-own-queue`                 | 1.00 | 0.00    |
| `glide-mq/never-enqueue-and-wait`           | 1.00 | 0.00    |
| `glide-mq/review-webhook-lane`              | 1.00 | 0.00    |
| `typescript-authoring/batch-first-wrapper`  | 0.33 | 0.00    |
| `typescript-authoring/rename-without-alias` | 0.67 | 0.00    |

- **Weak result:** `batch-first-wrapper` passes 1 of 3 with the plugin. The skill did not load on
  two attempts, and those answers failed the outcome grader. Without the skill, answers batch the
  I/O but leave the single-item function unchanged, so the wrapper requirement is what separates
  the arms. The skill's description was reworded twice to name per-item I/O, loops that enqueue,
  and bulk paths; loading did not become reliable.
- **Case history:** earlier versions let the model pass without the plugin (0.67 for a prompt
  that said the single-item helpers must keep working, and 1.00 and 0.67 for two others). A
  review framing loaded the skill on none of three attempts. The recorded version asks for the
  import in a write request and names the CRM's list-taking calls, so the open choice is the
  wrapper.
- `rename-without-alias` scored 1.00, 0.83, 0.67, 0.50, 0.83, and 0.67 across these runs on an
  unchanged case, while the skill's description changed; its description may load less often now.
  Not isolated.
- `glide-mq/review-webhook-lane` scored 0.00 without the plugin here, against 0.33 before.

## 2026-11-17: advisory locks softened from never to avoid, with a guard case

Sonnet agent, Opus judge, Claude Code 2.1.326. Three runs.

First, both arms of every `postgres-schema-design`, `glide-mq`, and
`postgres-node-performance-tuning` case: six cases, 36 attempts, $4.86.

| Case                                                           | With          | Without |
| -------------------------------------------------------------- | ------------- | ------- |
| `glide-mq/never-enqueue-and-wait`                              | 1.00          | 0.00    |
| `glide-mq/review-webhook-lane`                                 | 1.00          | 0.00    |
| `postgres-node-performance-tuning/new-query-named-and-indexed` | 1.00          | 0.00    |
| `postgres-schema-design/exact-cap-no-lock`                     | 1.00          | 0.00    |
| `postgres-schema-design/review-advisory-lock-key`              | 0.67 (1 of 3) | 1.00    |
| `postgres-schema-design/review-last-admin-advisory-lock`       | 0.83 (2 of 3) | 0.00    |

Then, after the grader change below, the three `postgres-schema-design` cases with the plugin
($1.45): `exact-cap-no-lock` 3 of 3, `review-advisory-lock-key` 0 of 3 (0.50),
`review-last-admin-advisory-lock` 1 of 3 (0.33). Then the same cases on the commit before this
change ($0.91): `exact-cap-no-lock` 3 of 3, `review-last-admin-advisory-lock` 1 of 3 (0.33).

- **The rule:** `postgres-schema-design` says to avoid advisory locks, not never use them. Prefer a
  constraint, a conditional write, or `SERIALIZABLE`; a change that takes one says why, and a
  review checks that it is transaction-scoped and that its key is specific to the operation.
  `postgres-node-performance-tuning` and `glide-mq` now say avoid and link there.
- **New guard case `review-advisory-lock-key`:** a justified lock whose key is shared with an
  unrelated lock. Its answers passed every attempt in both arms; every failure is `skill-fired`.
- **Grader change, `review-last-admin-advisory-lock`:** a review that recommends deleting the lock
  may also say how a kept lock should look. The failing attempt in the first run did exactly that,
  and two of three judges failed it for "a different lock key".
- **Load rate on 2.1.326:** in every failure of `review-last-admin-advisory-lock`, the skill did
  not load and the review grader failed. The commit before this change scored the
  same 1 of 3. Three attempts per revision cannot show whether the wording changed the load rate. On 2.1.325
  the case scored 3 of 3.

## 2026-11-17: a seven-day cooldown on every Dependabot ecosystem

Sonnet agent, Opus judge, Claude Code 2.1.326. Two three-attempt runs with both arms of every
`dependabot` case, $2.08 and $2.07. The table is the second run.

| Case                                 | With          | Without |
| ------------------------------------ | ------------- | ------- |
| `dependabot/groups-by-family`        | 1.00          | 0.33    |
| `dependabot/request-update-check`    | 1.00          | 0.50    |
| `dependabot/review-actions-cooldown` | 0.67 (2 of 3) | 0.67    |

- **The rule:** every update entry, including `github-actions` and `docker`, sets at least seven
  days, and so does every semver day key. A `pull_request` workflow on Dependabot pull requests
  may hold a write-capable secret only with that cooldown; otherwise split it into two stages.
- **New case `review-actions-cooldown`:** its first version left only the `github-actions` entry
  without a cooldown, and passed 3 of 3 without the plugin. The recorded version adds short
  `semver-patch-days` and a `docker` entry with none. It still separates the arms poorly: the one
  failure with the plugin is the skill not loading.
- In the first run, `request-update-check` scored 0.83 without the plugin.

## 2026-11-17: new skill `github-repository-settings`

Sonnet agent, Opus judge, Claude Code 2.1.326. One three-attempt run with both arms of the new
case: six attempts, $0.58.

| Case                                                | With | Without |
| --------------------------------------------------- | ---- | ------- |
| `github-repository-settings/collaborators-only-prs` | 1.00 | 0.67    |

- The skill covers the collaborators-only pull request setting and approval of every outside
  contributor's workflow runs, with read, set, and rollback commands.
- After the run, the Dependabot caveat changed from "not documented" to an observation: on two
  repositories, Dependabot opened a pull request 10 to 14 minutes after the setting was applied. The case does
  not grade that line; not re-measured.

## 2026-11-16: partition retention with survivors, and one job per reconciler stage

Sonnet agent, Opus judge. One three-attempt run with both arms of every `bounded-iteration` and
`postgres-partitioning-uuid-v7` case: four cases, 24 attempts, $3.34.

| Case                                                       | With | Without |
| ---------------------------------------------------------- | ---- | ------- |
| `bounded-iteration/review-dispatcher-predicate-and-job-id` | 1.00 | 1.00    |
| `bounded-iteration/review-staged-reconciler`               | 1.00 | 0.00    |
| `postgres-partitioning-uuid-v7/range-not-hash`             | 1.00 | 0.67    |
| `postgres-partitioning-uuid-v7/retention-with-survivors`   | 1.00 | 0.00    |

- **`postgres-partitioning-uuid-v7` gained a rule:** age-based retention drops whole UUIDv7 range
  partitions; a bounded `DELETE` sweep is allowed only when rows in an old range must survive, and
  the table comment says why.
- **`bounded-iteration` gained item 16 and a reference section:** each reconciler stage is its
  own job, with no shared mutable budget, error list, or cursor map, and ordered phases enqueue
  the next phase after commit.
- **New cases `retention-with-survivors` and `review-staged-reconciler`.** Without the plugin
  every attempt failed the outcome grader.
- The two older cases scored higher without the plugin than in their last two-arm runs (0.33 and
  0.00). Their skills changed in this commit, so the runs are not comparable.

## 2026-11-16: `static-code-analysis` adds complexity and five more checks; not re-measured

No run. `static-code-analysis` gained baseline rows for per-file complexity (`scc`, over 50
fails), dependency licenses, and known vulnerabilities (`osv-scanner`); JavaScript rows for React
lint plugins and for `publint` and `attw` on a published package; and rows for Python and Go. The
two `static-code-analysis` rows in `README.md` are not re-measured, and no case checks the new rows
yet.

## 2026-11-16: `npm-publishing` covers the release workflow; not re-measured

No run. `npm-publishing` now states what the publishing job needs for trusted publishing (npm
11.5.1 or newer, checked in the job; `id-token: write`; `registry-url` on `actions/setup-node`; no
npm token) and
links a new reference, `references/release-workflow.md`, with the job and the meaning of `E404`
and `ENEEDAUTH`. Its description now names fixing a CI publish that fails with either error. The
two `npm-publishing` rows in `README.md` are not re-measured. No case covers the new reference
yet.

## 2026-11-16: entry point rule added to `feature-rollout`, with two cases

Sonnet agent, Opus judge. A one-attempt pilot of the three `feature-rollout` cases passed ($0.31).
Then one three-attempt run with both arms, five cases, 30 attempts, $3.44.

| Case                                                     | With | Without |
| -------------------------------------------------------- | ---- | ------- |
| `feature-rollout/dark-route-without-caller`              | 1.00 | 1.00    |
| `feature-rollout/flag-defaults-on`                       | 1.00 | 0.00    |
| `feature-rollout/table-and-service-without-entry-point`  | 1.00 | 0.00    |
| `postgres-schema-design/exact-cap-no-lock`               | 0.67 | 0.00    |
| `postgres-schema-design/review-last-admin-advisory-lock` | 1.00 | 0.00    |

- **`feature-rollout` gained a section:** do not add a table, a service, or a write path that no
  registered entry point reaches. Its description now names a change that adds a table, a
  migration, a service, or a write path.
- **New case `table-and-service-without-entry-point`.** The prompt and the paths do not mention
  a flag or a rollout, so `skill-fired` checks the new trigger. Without the plugin no attempt
  asked for an entry point before merging.
- **New case `dark-route-without-caller` is a guard, not a discriminator.** It scores 1.00
  without the plugin. It is there so the new rule does not make a review reject a mounted,
  flagged route for having no caller. That bends the rule that a case is one the model gets
  wrong without the skill, so `AGENTS.md` now allows a guard case beside a discriminating one.
- **`postgres-schema-design` was run because the new trigger words overlap its description.**
  `exact-cap-no-lock` passed 1 of 3. In both failures the skill loaded and the answer still
  offered an exact cap (a `SERIALIZABLE` transaction, or an exact variant beside the best-effort
  one). A rerun of the two `postgres-schema-design` cases on the same commit, with the plugin
  only, passed 3 of 3 each ($1.04). No run on the commit before was made, so this does not show
  whether the description change had a part. The row in `README.md` is from the rerun.
- **Edited after the run, not re-measured:** both new prompts now say PostgreSQL 18, where
  `uuidv7()` is built in, and the skill counts a worker that subscribes to a queue as a
  registered job.
- The full suite was not rerun.

## 2026-11-15: lockfile conflict rule added to `git-commit-checklist`, with one case

Sonnet agent, Opus judge. One three-attempt run with both arms, four cases, 24 attempts, $2.56.

| Case                                               | With | Without |
| -------------------------------------------------- | ---- | ------- |
| `dependabot/groups-by-family`                      | 1.00 | 0.33    |
| `dependabot/request-update-check`                  | 1.00 | 0.50    |
| `git-commit-checklist/lockfile-conflict-on-rebase` | 1.00 | 0.00    |
| `git-commit-checklist/stages-only-task-files`      | 1.00 | 0.33    |

- **`git-commit-checklist` gained two rules:** let the package manager merge a conflicted
  lockfile and take neither side, and do not commit or push with `--no-verify`. Its description
  now names the lockfile conflict. `dependabot` gained one line that points to it.
- **New case `lockfile-conflict-on-rebase`.** Without the plugin, all three attempts pushed with
  `--no-verify` because the prompt asked to skip the hook, and at least one also took one side of
  the lockfile before reinstalling.
- **No pilot was run** before the measured run.
- **Edited after the run, not re-measured:** a review asked for a fallback when the package
  manager cannot parse a conflicted lockfile. The pnpm step the case checks is unchanged.
- The other three rows are reruns after the skill text changed. `dependabot/request-update-check`
  scored 0.50 without the plugin, against 0.67 in the entry before; nothing in that case changed.
- This is the first run of these skills since two earlier text changes that were not measured: a
  stacked pull request trigger rule in `github-actions-authoring` (not rerun here) and a wording
  change in `review-ci-logs` (not rerun here).

## 2026-11-15: six workflow skills added, with seven cases

Sonnet agent, Opus judge. Two one-attempt pilots with both arms ($2.52, $1.12), a three-attempt
run with both arms ($10.48), a three-attempt run with the plugin only ($5.37), six attempts of
`test-authoring` on this change and on the commit before it ($3.10, $3.18), and a three-attempt
rerun of two changed cases ($0.98).

| Case                                                    | With          | Without |
| ------------------------------------------------------- | ------------- | ------- |
| `agent-workflow/review-round-dispositions`              | 0.67 (2 of 3) | 0.00    |
| `git-commit-checklist/stages-only-task-files`           | 1.00          | 0.33    |
| `github-actions-authoring/required-check-is-a-real-job` | 1.00          | 0.00    |
| `github-issue/search-before-filing`                     | 0.83 (2 of 3) | 0.00    |
| `planning/plan-saved-outside-repo`                      | 1.00          | 0.00    |
| `review-ci-logs/quiet-log-keeps-errors`                 | 1.00          | 1.00    |
| `revisit-followups/only-verified-deferred-work`         | 1.00          | 0.00    |
| `test-authoring/dotnet-private-field-not-reflected`     | 0.83 (4 of 6) | 0.33    |
| `test-authoring/fake-routes-by-request`                 | 1.00 (6 of 6) | 0.00    |
| `test-authoring/fixture-files-not-inline`               | 1.00 (6 of 6) | 0.00    |
| `test-authoring/review-helper-holds-assertions`         | 0.75 (3 of 6) | 0.00    |

- **New skills:** `agent-workflow`, `git-commit-checklist`, `github-issue`, `planning`,
  `review-ci-logs`, `revisit-followups`. `github-actions-authoring` gained a workflow baseline
  reference and a wider description. `test-authoring` gained three rules on failure paths,
  evidence, and docs contracts.
- **Adding skills lowered how often `test-authoring` loads.** Six attempts per case, same hour:
  on the commit before, the skill loaded on 23 of 24 attempts; with the six skills added, 19 of 24. The drop is on the review case (6 of 6 to 3 of 6). On the attempts where it did not load,
  no skill was loaded at all, and the answer still passed. The sample is small.
- **`review-ci-logs` has no case the model fails unaided.** Two were tried: a retry that would
  mask a port collision, and a noisy log quieted by discarding stderr. Both scored 1.00 without
  the plugin. The second is kept so the skill has a case.
- **`git-commit-checklist`:** a case on a force push that names the expected remote tip scored
  1.00 without the plugin in the pilot and was replaced by the staging case.
- **`github-actions-authoring`:** a review case on a draft-transition trigger and cancellation
  on the default branch scored 1.00 without the plugin in the pilot and was replaced.
- **`agent-workflow` prompt changed after the first measured run.** It now says the repository
  has no job queue and that the teammate's comment was checked, because answers hedged on both.
  The "Without" score predates that edit. In the rerun the one failed attempt did not load the
  skill and implemented more than the blocking item.
- **`github-issue`:** its description lost the label and project wording after the two-arm run.
  One of three attempts in the rerun loaded the skill and still failed the answer grader.
- **`fake-routes-by-request` grader:** it failed two answers that routed the fake by path and
  also asserted which paths were requested. That assertion is now allowed; asserting call order
  still fails.
- **`pr-description`** gained rules for diagrams, activation, cost, schema inventories, folded
  detail, and local check gaps, plus an examples reference. Its case was rerun with the plugin
  only ($0.28): 1.00, 3 of 3. The new rules have no case of their own.
- **`scripts/eval-summary.py`** now counts every grader in the score, so a two-arm run and a
  plugin-only run report the same thing.

## 2026-11-15: three `test-authoring` cases added, `dependabot` skill trimmed

Sonnet agent, Opus judge. Two one-attempt pilots with both arms ($1.28, $0.84), a three-attempt
run with both arms ($4.65), then a three-attempt run of `test-authoring` with the plugin only
($1.50). One earlier `dependabot` run is discarded: the skill file changed under it.

| Case                                                | With | Without |
| --------------------------------------------------- | ---- | ------- |
| `dependabot/groups-by-family`                       | 1.00 | 0.33    |
| `dependabot/request-update-check`                   | 1.00 | 0.67    |
| `test-authoring/dotnet-private-field-not-reflected` | 1.00 | 0.33    |
| `test-authoring/fake-routes-by-request`             | 1.00 | 0.00    |
| `test-authoring/fixture-files-not-inline`           | 1.00 | 0.00    |
| `test-authoring/review-helper-holds-assertions`     | 1.00 | 0.00    |

- **`dependabot` skill:** the rules for an auto-merge workflow are removed, and the description
  no longer mentions one. No case or grader changed. Both cases were re-measured in the two-arm
  run; the skill loaded on all six with-plugin attempts.
- **New cases** cover the reference files that had none: `dotnet.md` (no reflection on a private
  field), `dependency-boundaries.md` (a fake that answers by request, not by call order), and
  the helper and re-implementation rules in `SKILL.md` and `tautological-tests.md`.
- **Tried and dropped,** each 1.00 without the plugin in the first pilot: a test broken by a
  dependency upgrade that pinned the dependency's message, a Vitest module mock that keeps the
  real exports, and tests for a module-private function.
- **The .NET prompt was softened after the first pilot.** It said "I want to check the
  dictionary itself this time", and both arms used reflection as asked. That sentence is gone.
- **Three graders were tightened in review, then re-run** with the plugin only ($1.35, 12 of 12).
  The .NET grader accepts a `Count` check only when the test also shows the live session
  remains. The fake grader no longer asks the fake to survive a new request. The review grader
  no longer claims to know the production formula. The "Without" column predates these edits.
- **`vitest.md`, `storybook.md`, and `swift.md` still have no case.**
- **Scoring in a two-arm run:** `claude plugin eval` leaves the `skill-fired` grader out of the
  score when it runs both arms. In the two-arm run the skill did not load on one of three
  attempts for the .NET case and for the review case, and both still scored 1.00. The "With"
  column for `test-authoring` is from the plugin-only run, where the skill loaded on all 12
  attempts. Earlier "With" scores taken from two-arm runs may hide a skill that did not load.

## 2026-11-15: runner rules folded into `test-authoring`

Sonnet agent, Opus judge, three attempts. Two runs: one with both arms ($5.44), then one with the
plugin only ($0.85) after the layout changed.

| Case                                                  | With | Without |
| ----------------------------------------------------- | ---- | ------- |
| `test-authoring/fixture-files-not-inline`             | 1.00 | 0.00    |
| `playwright-authoring/metadata-is-not-a-browser-test` | 1.00 | 0.00    |

- Rules for Vitest (including backend and Next.js), Storybook, Swift, and .NET tests were added.
  The first layout was four new skills, one per runner, each with one case.
- That layout was abandoned. All four new cases scored 1.00 with the plugin and 1.00 without, so
  they showed nothing. And `fixture-files-not-inline` fell to 0.33 (1 of 3): on two attempts
  `test-authoring` did not load, because a runner skill matched the prompt instead.
- The runner rules are now reference files under `skills/test-authoring/references/`, and
  `test-authoring` is the one skill that fires for any test. Its description names the runners.
  The four runner skills and their four cases are removed.
- `fixture-files-not-inline` is back to 3 of 3 in the second run. The "Without" column is from the
  first run; the second run had no no-plugin arm.
- The runner reference files have no case of their own. A case needs a decision the model gets
  wrong unaided, and the four tried (shared-database test identity, a story for a server
  component, a delayed `URLProtocol` double, a separate portable test project) were not that.

## 2026-11-15: review fixes to two replaced cases, re-measured

Sonnet agent, Opus judge, three attempts per arm, $1.73.

| Case                                                           | With          | Without |
| -------------------------------------------------------------- | ------------- | ------- |
| `bounded-iteration/review-dispatcher-predicate-and-job-id`     | 0.67 (2 of 3) | 0.33    |
| `postgres-node-performance-tuning/new-query-named-and-indexed` | 1.00          | 0.00    |

- The dispatcher prompt's job id used a colon, which the queue library rejects in a custom id. It
  now uses a hyphen.
- The query grader now also requires the account and status filters, newest-first ordering, and
  `id` as a tie-breaker in the ordering and the cursor.
- On the dispatcher case the `bounded-iteration` skill did not load on one of three attempts, and
  that attempt's review missed the job id. Across the three with-plugin runs of this case the
  skill loaded on 7 of 9 attempts. The description was not changed; watch it on the next full
  run. `README.md` carries this run's row.

## 2026-11-15: three ceiling cases replaced

Sonnet agent, Opus judge, three attempts per arm, five targeted runs ($2.80, $2.64, $1.59, $0.86,
and one stopped early). The three cases that scored 1.00 without the plugin are replaced.

| Case                                                           | With | Without |
| -------------------------------------------------------------- | ---- | ------- |
| `bounded-iteration/review-dispatcher-predicate-and-job-id`     | 1.00 | 0.00    |
| `postgres-node-performance-tuning/new-query-named-and-indexed` | 1.00 | 0.00    |
| `postgres-schema-design/review-last-admin-advisory-lock`       | 1.00 | 0.00    |

- **`new-query-named-and-indexed`** keeps the prompt of `new-query-shape-gets-index`. The grader
  now also requires the operation-name comment on the statement, which the model does not write
  on its own.
- **`review-dispatcher-predicate-and-job-id`** replaces `review-sweep-until-empty`. The model
  already asks for a cap on an unbounded loop. The new diff is capped and tests two things the
  model misses: a predicate no index can serve, and a job id keyed only by the entity. The first
  version of the prompt described the queue's deduplication and scored 1.00 without the plugin;
  naming the queue library instead brought that to 0.33. A review then pointed out that the
  grader's claim "no index can serve the predicate" is wrong: a partial index with the same
  predicate can. The grader now accepts that remedy, the prompt states the table's indexes, and
  a fifth run ($0.86) scored 1.00 with and 0.00 without.
- **`review-last-admin-advisory-lock`** replaces `review-parent-row-lock`. Two things changed
  before it passed:
  - The skill did not load on any of the first three attempts, and the agent approved the lock.
    The `postgres-schema-design` description now names row locks, advisory locks, serializing
    writers, and last-one guards. It loaded on 5 of 6 attempts after.
  - The grader failed answers that removed the lock and then offered `SERIALIZABLE` with `40001`
    as 409 if the guard must be exact. The skill allows that, so the grader now does too.
- The three `README.md` rows are from these runs, not from the 33-case run.

## 2026-11-15: `structured-logging` case replaced again, skill extended

Sonnet agent, Opus judge, three attempts per arm.

| Case                                           | With | Without | Cost  |
| ---------------------------------------------- | ---- | ------- | ----- |
| `structured-logging/add-refund-logs`           | 1.00 | 1.00    | $0.79 |
| `structured-logging/log-identifier-not-lookup` | 1.00 | 0.00    | $0.76 |

- A review pointed out that `review-refund-batch-logs`, the skill's only case, passed without the
  plugin. A second attempt, `add-refund-logs`, also passed without it: the model writes a static
  message with fields on its own. Both are removed.
- What the model does not do on its own is decline a lookup when asked to log a value behind one.
  The skill gained a section for that (log the identifier, add no queries for a log line, keep
  personal data out of logs), and `log-identifier-not-lookup` uses the prompt of the first,
  abandoned case. With the earlier skill text that prompt scored 0.00 in both arms.
- The `structured-logging` row in `README.md` is from this run. It was not part of the 33-case
  run below, which measured `review-refund-batch-logs`.

## 2026-11-15: full single-arm run, 33 cases

Sonnet agent, Opus judge, three attempts, with the plugin only. Claude Code 2.1.325, 99 attempts,
$15.38. Run on the change that added the twelve rule skills.

- All 33 cases scored 1.00, 3 of 3.
- `glide-mq/never-enqueue-and-wait` and `static-code-analysis/new-typescript-library`, which each
  missed one attempt in the 21-case run, passed all three here. Nothing about them changed.

## 2026-11-15: twelve rule skills, with and without the plugin

Sonnet agent, Opus judge, three attempts per arm. First run of the twelve new cases ($8.26), then
a rerun of the five that changed ($3.22). Rows marked "rerun" are from the second run.

| Case                                                  | With | Without | Run   |
| ----------------------------------------------------- | ---- | ------- | ----- |
| `documentation/review-readme-drift`                   | 1.00 | 0.00    | rerun |
| `feature-rollout/flag-defaults-on`                    | 1.00 | 0.00    | rerun |
| `http-api-design/action-body-and-raw-tokens`          | 1.00 | 0.33    | first |
| `native-app-architecture/core-tests-and-web-view`     | 1.00 | 0.00    | first |
| `playwright-authoring/metadata-is-not-a-browser-test` | 1.00 | 0.00    | rerun |
| `pr-description/perf-change-with-flag`                | 1.00 | 0.00    | first |
| `react-app-structure/review-profile-card`             | 1.00 | 0.33    | first |
| `rust-authoring/tests-in-separate-file`               | 1.00 | 0.00    | rerun |
| `service-boundaries/partner-webhook`                  | 1.00 | 0.33    | first |
| `structured-logging/review-refund-batch-logs`         | 1.00 | 1.00    | rerun |
| `test-authoring/fixture-files-not-inline`             | 1.00 | 0.00    | first |
| `typescript-authoring/rename-without-alias`           | 1.00 | 0.00    | first |

Changes between the first run and the rerun:

- **`structured-logging` case replaced.** The first case, `refund-failure-log`, scored 0.00 in
  both arms. Its prompt asked for the customer's email in the log line, which the skill tells the
  agent not to fetch, so the agent followed the request. That was a badly posed case, not a skill
  failure. The replacement is a review of a diff. It scores 1.00 without the plugin, so it is a
  ceiling case and needs a harder prompt.
- **`playwright-authoring` case replaced.** `redirect-is-not-a-browser-test` scored 1.00 without
  the plugin: a prompt that says "301" leads to an HTTP test on its own. The replacement adds page
  metadata checks and does not say how to test them.
- **`rust-authoring/tests-in-separate-file` prompt shortened.** The sentence "Show me exactly what
  goes in which file" hinted at a separate file; the case scored 1.00 without the plugin. Without
  the sentence it scores 0.00.
- **`feature-rollout/flag-defaults-on` grader narrowed.** It required the answer to say the deploy
  is "confirmed healthy" before enabling. One attempt set the flag off, kept the fallback off, and
  separated deployed from enabled, and failed on that wording alone. The grader now asks only that
  enabling is a separate step.
- **`documentation` description extended.** The skill did not load on 2 of 3 attempts of a diff
  review. The description now names a diff that changes docs or comments. It loaded 3 of 3 after.

## 2026-11-15: no-plugin arm for the two `evals` cases

Sonnet agent, Opus judge, three attempts per arm, `EVAL_ABLATION=with-without`, $2.10.

| Case                         | With | Without |
| ---------------------------- | ---- | ------- |
| `evals/document-new-suite`   | 1.00 | 0.00    |
| `evals/record-partial-rerun` | 1.00 | 0.00    |

Both cases fail without the plugin on all three attempts, so neither is a ceiling case.

## 2026-11-15: first full single-arm run, 21 cases

Sonnet agent, Opus judge, three attempts, with the plugin only. Claude Code 2.1.325, 63 attempts,
$10.55. Run on the change that added the `evals` skill, on top of `baf9931`.

- 19 of 21 cases passed all three attempts. This is the first run in which every row comes from
  one run, and the first to measure the dependabot cases after the skill's last edit and
  `npm-publishing/existing-package-trust-only` after its grader was restored. All four scored
  1.00.
- `glide-mq/never-enqueue-and-wait` 0.83 (2 of 3). The failing attempt returned 202 and polled,
  but kept the job on the shared `background` queue in a separate ordering lane. The grader asks
  for its own queue, as the skill does. Not changed; watch it on the next run.
- `static-code-analysis/new-typescript-library` 0.89 (2 of 3). On one attempt the judge voted
  1 pass to 2 fails on the list of checks. Not changed.
- New cases `evals/document-new-suite` and `evals/record-partial-rerun` scored 1.00. Their
  no-plugin arm is in the entry above.
- The new Markdown files under `evals/` were not picked up as cases: the runner found 21.

## 2026-11-15: default run drops the no-plugin arm

- `pnpm run evals` now runs with the plugin only (`--ablation none`). The no-plugin arm is opt-in
  with `EVAL_ABLATION=with-without`, for checking a new case.
- Under a single arm the `skill-fired` grader counts toward a case's score. In the entries below
  it was an indicator outside the score, so a with-plugin score here can be lower than there for
  the same answers.
- Added `evals/README.md`, `evals/HISTORY.md`, and `evals/AGENTS.md`, and the `evals` skill with
  two cases: `evals/document-new-suite` and `evals/record-partial-rerun`.

## 2026-11-15: database and queue skills, with and without the plugin

Sonnet agent, Opus judge, three attempts per arm, on the commit that added the five database and
queue skills.

| Case                                                          | With | Without |
| ------------------------------------------------------------- | ---- | ------- |
| `postgres-schema-design/exact-cap-no-lock`                    | 1.00 | 0.00    |
| `postgres-schema-design/review-parent-row-lock`               | 1.00 | 1.00    |
| `postgres-node-performance-tuning/new-query-shape-gets-index` | 1.00 | 1.00    |
| `postgres-partitioning-uuid-v7/range-not-hash`                | 1.00 | 0.00    |
| `bounded-iteration/review-sweep-until-empty`                  | 1.00 | 1.00    |
| `glide-mq/never-enqueue-and-wait`                             | 1.00 | 0.00    |
| `glide-mq/review-webhook-lane`                                | 1.00 | 0.33    |
| `github-actions-authoring/publish-gated-on-ci`                | 1.00 | 0.00    |
| `github-actions-authoring/rejects-polling`                    | 1.00 | 0.67    |
| `package-json-checklist/workspace-dependency-and-export`      | 1.00 | 0.67    |

- Three cases score 1.00 without the plugin: `review-parent-row-lock`,
  `new-query-shape-gets-index`, and `review-sweep-until-empty`. They show that the skill fires and
  does no harm, not that it changes the answer. They need harder prompts.
- `review-sweep-until-empty` was written here. The benchmark case it stands in for was not
  available.

## 2026-11-15: first seven skills, with and without the plugin

Sonnet agent, Opus judge, three attempts per arm. The rows come from more than one run: the
dependabot, npm-publishing, and triage rows are from a rerun after the last grader and fixture
changes, the rest from the last full run.

| Case                                                     | With | Without |
| -------------------------------------------------------- | ---- | ------- |
| `dependabot/groups-by-family`                            | 1.00 | 0.33    |
| `dependabot/request-update-check`                        | 1.00 | 0.33    |
| `github-actions-authoring/rejects-polling`               | 1.00 | 0.50    |
| `npm-publishing/bootstrap-new-package`                   | 1.00 | 0.40    |
| `npm-publishing/existing-package-trust-only`             | 1.00 | 0.73    |
| `package-json-checklist/workspace-dependency-and-export` | 1.00 | 0.67    |
| `static-analysis-checklist/parses-yaml-with-fixtures`    | 1.00 | 0.50    |
| `static-code-analysis/new-rust-service`                  | 1.00 | 0.83    |
| `static-code-analysis/new-typescript-library`            | 1.00 | 0.00    |
| `triage-codex-security/approval-wave-cap`                | 1.00 | 0.50    |
| `triage-codex-security/repository-mismatch`              | 1.00 | 0.78    |

Changes during this batch that moved scores:

- **`triage-codex-security/approval-wave-cap` fixture replaced.** It scored 0.67 with the plugin
  (7 of 9 attempts across earlier runs). Its findings had arguable verdicts; they were replaced
  with unambiguous ones, and the case then passed 3 of 3.
- **Graders anchored to the Bash `"command"` input.** Patterns over the whole transcript matched
  the prompt and the fixture.
- **`npm-publishing/existing-package-trust-only` grader widened, then reverted.** A review
  suggestion removed the `--otp` anchor from `no-publish-command`. The case dropped to 0.80
  because the fixture's own `release.yml` contains an `npm publish` line. The anchor was
  restored and checked by pattern against three kept traces; the case was not re-run in this
  batch.
- **Dependabot skill edited after its last run** (eligibility is rechecked on every new head).
  The two dependabot rows predate that edit.
