# Evals

**Does an agent that has this plugin follow each skill's guidance on a realistic request?**

Each case gives an agent a request a person would make, such as "review this diff" or "write this
workflow", without naming a skill. Graders then check two things: that the agent loaded the right
skill, and that its answer made the decision the skill asks for.

- Rules for adding and running cases: [AGENTS.md](AGENTS.md)
- Every earlier run and every change to a case or grader: [HISTORY.md](HISTORY.md)

## Current results

Agent Sonnet, judge Opus, Claude Code 2.1.330. 75 cases.

- **Run:** the full run on 2026-11-21, with the plugin only, three attempts per case (78 cases,
  234 attempts, $35.50). It ran on the working tree committed as `9f4aaf5`, before the
  `http-api-design` description named edge workers. Four of its cases were dropped afterwards.
- **Rows marked "(rerun)"** are from later two-arm runs, three attempts per arm: the edge-claims
  and topology rows after the `http-api-design` change, and five new cases after their graders
  were narrowed in review. The `postgres-schema-design` rows are from the two-arm run after the
  lock exception was added; the last-admin row is from a six-attempt plugin-only rerun after the
  exception named caps.
- **"Without plugin"** comes from separate runs; see "How to read the numbers".
- **The skill loaded in 224 of the 234 attempts.** The misses were in four cases:
  `documentation/write-scale-numbers` (0 of 3, then 2 of 3 on a rerun), the edge-claims case (0
  of 3, before its description named edge workers), `bounded-iteration/review-dispatcher-predicate-and-job-id`
  (1 of 3), and `postgres-schema-design/review-advisory-lock-key` (1 of 3). In the rerun,
  `service-boundaries/topology-in-app-repo` loaded it in 1 of 3. In every miss the agent called no
  skill at all; it did not pick a wrong one.
- **Rows below 1.00 for other reasons** failed on the answer: `git-commit-checklist/lockfile-conflict-on-rebase`,
  `github-issue/search-before-filing`, `npm-publishing/bootstrap-new-package`,
  `package-json-checklist/workspace-dependency-and-export`, `revisit-followups/only-verified-deferred-work`,
  and `technical-writing/proposal-for-two-audiences`.
- **The 10 cases added on 2026-11-21** took their "Without plugin" score from a one-attempt pilot,
  except the rerun rows.
- **Not measured without the plugin:** the five rows marked `n/a`.

| Case                                                               | Score | Passed | Without plugin |
| ------------------------------------------------------------------ | ----- | ------ | -------------- |
| `agent-workflow/review-round-dispositions`                         | 1.00  | 3 of 3 | 0.00           |
| `bounded-iteration/review-dispatcher-predicate-and-job-id`         | 0.50  | 1 of 3 | 1.00           |
| `bounded-iteration/review-staged-reconciler`                       | 1.00  | 3 of 3 | 0.00           |
| `dependabot/groups-by-family`                                      | 1.00  | 3 of 3 | 0.33           |
| `dependabot/request-update-check`                                  | 1.00  | 3 of 3 | 0.50           |
| `dependabot/review-actions-cooldown`                               | 1.00  | 3 of 3 | 0.67           |
| `documentation/review-readme-drift`                                | 1.00  | 3 of 3 | 0.67           |
| `documentation/write-scale-numbers`                                | 0.00  | 0 of 3 | 0.00           |
| `evals/document-new-suite`                                         | 1.00  | 3 of 3 | 0.00           |
| `evals/record-partial-rerun`                                       | 1.00  | 3 of 3 | 0.00           |
| `feature-rollout/dark-route-without-caller`                        | 1.00  | 3 of 3 | 1.00           |
| `feature-rollout/flag-defaults-on`                                 | 1.00  | 3 of 3 | 0.00           |
| `feature-rollout/table-and-service-without-entry-point`            | 1.00  | 3 of 3 | 0.00           |
| `git-commit-checklist/lockfile-conflict-on-rebase`                 | 0.83  | 2 of 3 | 0.00           |
| `git-commit-checklist/stages-only-task-files`                      | 1.00  | 3 of 3 | 0.33           |
| `github-actions-authoring/no-actions-write-with-checked-out-code`  | 1.00  | 3 of 3 | n/a            |
| `github-actions-authoring/publish-gated-on-ci`                     | 1.00  | 3 of 3 | 0.00           |
| `github-actions-authoring/rejects-polling`                         | 1.00  | 3 of 3 | 0.67           |
| `github-actions-authoring/required-check-is-a-real-job`            | 1.00  | 3 of 3 | 0.00           |
| `github-issue/search-before-filing`                                | 0.83  | 2 of 3 | 0.00           |
| `github-issue/stale-notes-before-filing` (rerun)                   | 1.00  | 3 of 3 | 0.33           |
| `github-repository-settings/collaborators-only-prs`                | 1.00  | 3 of 3 | 0.67           |
| `github-repository-settings/opentofu-provider-gap`                 | 1.00  | 3 of 3 | 0.00           |
| `glide-mq/failed-jobs-need-recovery-path`                          | 1.00  | 3 of 3 | n/a            |
| `glide-mq/never-enqueue-and-wait`                                  | 1.00  | 3 of 3 | 0.00           |
| `glide-mq/outbox-own-queue`                                        | 1.00  | 3 of 3 | 0.00           |
| `glide-mq/review-webhook-lane`                                     | 1.00  | 3 of 3 | 0.00           |
| `http-api-design/action-body-and-raw-tokens`                       | 1.00  | 3 of 3 | 0.33           |
| `http-api-design/edge-claims-do-not-authorize` (rerun)             | 1.00  | 3 of 3 | 0.00           |
| `native-app-architecture/core-tests-and-web-view`                  | 1.00  | 3 of 3 | 0.00           |
| `native-app-architecture/generated-project-file` (rerun)           | 1.00  | 3 of 3 | 0.00           |
| `npm-publishing/bootstrap-new-package`                             | 0.94  | 2 of 3 | 0.40           |
| `npm-publishing/existing-package-trust-only`                       | 1.00  | 3 of 3 | 0.73           |
| `package-json-checklist/review-override-for-peer-warning` (rerun)  | 1.00  | 3 of 3 | 0.67           |
| `package-json-checklist/workspace-dependency-and-export`           | 0.89  | 2 of 3 | 0.67           |
| `planning/plan-saved-outside-repo`                                 | 1.00  | 3 of 3 | 0.00           |
| `playwright-authoring/metadata-is-not-a-browser-test`              | 1.00  | 3 of 3 | 0.00           |
| `playwright-authoring/review-computed-test-ids` (rerun)            | 1.00  | 3 of 3 | 0.00           |
| `postgres-node-performance-tuning/index-change-rechecks-neighbors` | 1.00  | 3 of 3 | n/a            |
| `postgres-node-performance-tuning/new-query-named-and-indexed`     | 1.00  | 3 of 3 | 0.00           |
| `postgres-partitioning-uuid-v7/range-not-hash`                     | 1.00  | 3 of 3 | 0.67           |
| `postgres-partitioning-uuid-v7/retention-with-survivors`           | 1.00  | 3 of 3 | 0.00           |
| `postgres-schema-design/checkout-exact-stock` (rerun)              | 1.00  | 3 of 3 | 0.67           |
| `postgres-schema-design/exact-cap-no-lock` (rerun)                 | 1.00  | 3 of 3 | 0.00           |
| `postgres-schema-design/rollup-trigger-deadlocks` (rerun)          | 1.00  | 3 of 3 | 0.00           |
| `postgres-schema-design/review-advisory-lock-key` (rerun)          | 0.67  | 1 of 3 | 1.00           |
| `postgres-schema-design/review-last-admin-advisory-lock` (rerun)   | 1.00  | 6 of 6 | 0.00           |
| `pr-description/ci-gap-section`                                    | 1.00  | 3 of 3 | 0.00           |
| `pr-description/perf-change-with-flag`                             | 1.00  | 3 of 3 | 0.00           |
| `react-app-structure/abort-superseded-search`                      | 1.00  | 3 of 3 | n/a            |
| `react-app-structure/review-profile-card`                          | 1.00  | 3 of 3 | 0.33           |
| `review-ci-logs/quiet-log-keeps-errors`                            | 1.00  | 3 of 3 | 1.00           |
| `revisit-followups/only-verified-deferred-work`                    | 0.83  | 2 of 3 | 0.00           |
| `rust-authoring/tests-in-separate-file`                            | 1.00  | 3 of 3 | 0.00           |
| `service-boundaries/partner-webhook`                               | 1.00  | 3 of 3 | 0.33           |
| `service-boundaries/topology-in-app-repo` (rerun)                  | 0.33  | 1 of 3 | 0.00           |
| `static-analysis-checklist/parses-yaml-with-fixtures`              | 1.00  | 3 of 3 | 0.50           |
| `static-analysis-checklist/tracked-files-only`                     | 1.00  | 3 of 3 | 0.00           |
| `static-code-analysis/new-rust-service`                            | 1.00  | 3 of 3 | 0.83           |
| `static-code-analysis/new-typescript-library`                      | 1.00  | 3 of 3 | 0.00           |
| `structured-logging/log-identifier-not-lookup`                     | 1.00  | 3 of 3 | 0.00           |
| `structured-logging/review-lookup-in-catch` (rerun)                | 1.00  | 3 of 3 | 0.00           |
| `technical-writing/incident-writeup-from-notes`                    | 1.00  | 3 of 3 | 0.00           |
| `technical-writing/proposal-for-two-audiences`                     | 0.67  | 1 of 3 | 0.00           |
| `technical-writing/review-runbook-wall-of-text`                    | 1.00  | 3 of 3 | 0.00           |
| `test-authoring/dotnet-private-field-not-reflected`                | 1.00  | 3 of 3 | 0.33           |
| `test-authoring/fake-routes-by-request`                            | 1.00  | 3 of 3 | 1.00           |
| `test-authoring/fixture-files-not-inline`                          | 1.00  | 3 of 3 | 0.00           |
| `test-authoring/review-direct-row-inserts`                         | 1.00  | 3 of 3 | 0.67           |
| `test-authoring/review-helper-holds-assertions`                    | 1.00  | 3 of 3 | 0.00           |
| `triage-codex-security/approval-wave-cap`                          | 1.00  | 3 of 3 | 0.50           |
| `triage-codex-security/repository-mismatch`                        | 1.00  | 3 of 3 | 0.78           |
| `typescript-authoring/batch-first-wrapper`                         | 1.00  | 3 of 3 | 0.00           |
| `typescript-authoring/rename-without-alias`                        | 1.00  | 3 of 3 | 0.00           |
| `typescript-authoring/unlink-batched-cache-delete`                 | 1.00  | 3 of 3 | n/a            |

"Without plugin" is the case's last measured score with no plugin installed. Most come from
separate, earlier runs with three attempts each, and some from the same two-arm run as the score; [HISTORY.md](HISTORY.md) has the run for each.

A score is the mean over a case's attempts. An attempt's score is the share of its graders that
passed, so 1.00 means every grader passed on every attempt.

## What each case checks

| Case                                                               | Checks that the agent                                                                                              |
| ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| `agent-workflow/review-round-dispositions`                         | Gives each review comment one disposition and settles the round with a single push.                                |
| `bounded-iteration/review-dispatcher-predicate-and-job-id`         | Flags a dispatcher predicate no index can serve and a job id keyed only by the entity.                             |
| `bounded-iteration/review-staged-reconciler`                       | Asks for one job per reconciler stage, with no shared budget, error list, or cursor map.                           |
| `dependabot/groups-by-family`                                      | Groups updates by package family with a cooldown, not by version type or a catch-all.                              |
| `dependabot/request-update-check`                                  | Sends the user to the web page that starts a Dependabot run, and invents no API call.                              |
| `dependabot/review-actions-cooldown`                               | Asks for at least seven days on every ecosystem and semver key when a Dependabot workflow holds a write token.     |
| `documentation/review-readme-drift`                                | Flags restated config values, history and plans in a README, and a comment that explains history.                  |
| `documentation/write-scale-numbers`                                | Writes how to fetch changing numbers into a README section, not the numbers.                                       |
| `evals/document-new-suite`                                         | Proposes a README, a history log, and an agent rules file for a new eval suite.                                    |
| `evals/record-partial-rerun`                                       | Logs a grader change and does not report a one-attempt rerun as an improvement.                                    |
| `feature-rollout/dark-route-without-caller`                        | Guard: accepts a mounted route behind an off flag that no page calls yet.                                          |
| `feature-rollout/flag-defaults-on`                                 | Defaults a new flag to off and reports deployed separately from enabled.                                           |
| `feature-rollout/table-and-service-without-entry-point`            | Asks for a registered entry point before a table and a service merge.                                              |
| `git-commit-checklist/lockfile-conflict-on-rebase`                 | Lets pnpm merge a conflicted lockfile, takes neither side, and keeps the pre-push hook.                            |
| `git-commit-checklist/stages-only-task-files`                      | Stages the files of the fix by name and leaves unrelated and generated changes out.                                |
| `github-actions-authoring/no-actions-write-with-checked-out-code`  | Keeps `actions: write` out of any job that runs checked-out code.                                                  |
| `github-actions-authoring/publish-gated-on-ci`                     | Gates a publish on CI with `needs` or `workflow_run`, not a status lookup.                                         |
| `github-actions-authoring/rejects-polling`                         | Replaces a sleep-and-poll loop with an event trigger.                                                              |
| `github-actions-authoring/required-check-is-a-real-job`            | Builds a combined required check as a fan-in job, not a posted commit status.                                      |
| `github-issue/search-before-filing`                                | Searches for a duplicate before filing, writes a self-contained body, and invents no label.                        |
| `github-issue/stale-notes-before-filing`                           | Checks that a months-old note still holds on the default branch before filing it.                                  |
| `github-repository-settings/collaborators-only-prs`                | Limits pull requests to collaborators and requires approval for every outside contributor's runs.                  |
| `github-repository-settings/opentofu-provider-gap`                 | Says the OpenTofu provider has no attribute for two settings and gives the REST calls.                             |
| `glide-mq/failed-jobs-need-recovery-path`                          | Adds a recovery path for permanently failed jobs beyond the dead-letter queue.                                     |
| `glide-mq/never-enqueue-and-wait`                                  | Returns 202 with a job id instead of waiting on the job inside the request.                                        |
| `glide-mq/outbox-own-queue`                                        | Puts an outbox relay and a ledger write on a queue of their own, not a lane on the shared queue.                   |
| `glide-mq/review-webhook-lane`                                     | Gives a floodable webhook job family its own lane.                                                                 |
| `http-api-design/action-body-and-raw-tokens`                       | Uses resource paths, the public id, and hashed tokens.                                                             |
| `http-api-design/edge-claims-do-not-authorize`                     | Uses an edge-verified claim for cache bypass only, and lets the origin authorize.                                  |
| `native-app-architecture/core-tests-and-web-view`                  | Runs portable core tests once and asks for a written blocker before a web view.                                    |
| `native-app-architecture/generated-project-file`                   | Commits the project specification and ignores the generated Xcode project.                                         |
| `npm-publishing/bootstrap-new-package`                             | Prepares a first publish for a person to run, and never publishes itself.                                          |
| `npm-publishing/existing-package-trust-only`                       | Sets up trusted publishing for an existing package without publishing or changing trust.                           |
| `package-json-checklist/review-override-for-peer-warning`          | Accepts React 19 for one package with `peerDependencyRules`, not an override.                                      |
| `package-json-checklist/workspace-dependency-and-export`           | Adds dependencies with pnpm in the right fields and keeps the root export.                                         |
| `planning/plan-saved-outside-repo`                                 | Saves a plan in an issue or pull request, not a committed file, and weighs an alternative.                         |
| `playwright-authoring/metadata-is-not-a-browser-test`              | Checks redirects and metadata over plain HTTP and locates a button by role.                                        |
| `playwright-authoring/review-computed-test-ids`                    | Asks for literal test ids and `getByTestId` with a row filter, not computed ids.                                   |
| `postgres-node-performance-tuning/index-change-rechecks-neighbors` | Rechecks every query that depends on a changed index, found through the query builders.                            |
| `postgres-node-performance-tuning/new-query-named-and-indexed`     | Ships a new query with explicit columns, a bound, an operation-name comment, and its index.                        |
| `postgres-partitioning-uuid-v7/range-not-hash`                     | Partitions by `RANGE` on the id with a default partition, not `HASH`.                                              |
| `postgres-partitioning-uuid-v7/retention-with-survivors`           | Keeps a bounded delete sweep when old rows must survive, and states the reason in a table comment.                 |
| `postgres-schema-design/checkout-exact-stock`                      | Guard: checkout keeps stock exact without an unbounded lock, and enqueues after commit.                            |
| `postgres-schema-design/exact-cap-no-lock`                         | Enforces a cap as best effort, with no lock, counter, or claim table.                                              |
| `postgres-schema-design/review-advisory-lock-key`                  | Guard: checks the key of an advisory lock the author justified, and does not reject it on principle.               |
| `postgres-schema-design/review-last-admin-advisory-lock`           | Asks for an advisory lock around a last-admin guard to be deleted, leaving it best effort.                         |
| `postgres-schema-design/rollup-trigger-deadlocks`                  | Replaces a trigger-kept daily rollup that deadlocks with closed days computed once, not ordered or advisory locks. |
| `pr-description/ci-gap-section`                                    | Adds a short section on what CI caught that the local check missed, without work history.                          |
| `pr-description/perf-change-with-flag`                             | Shows a before/after table, states what a flag does not cover, and omits routine checks.                           |
| `react-app-structure/abort-superseded-search`                      | Aborts a superseded search request with an `AbortController`.                                                      |
| `react-app-structure/review-profile-card`                          | Objects to a user-supplied image rendered directly, a computed fetch URL, and a hand-built link.                   |
| `review-ci-logs/quiet-log-keeps-errors`                            | Quiets a noisy log at its source and keeps stderr and failing exits.                                               |
| `revisit-followups/only-verified-deferred-work`                    | Keeps only explicit deferred work and has it verified before any issue is filed.                                   |
| `rust-authoring/tests-in-separate-file`                            | Puts unit tests for a private function in a separate file.                                                         |
| `service-boundaries/partner-webhook`                               | Maps a partner payload once, keeps the raw body, and tests with captured payloads.                                 |
| `service-boundaries/topology-in-app-repo`                          | Moves instance counts and regions out of the application repo into configuration.                                  |
| `static-analysis-checklist/parses-yaml-with-fixtures`              | Parses YAML instead of matching text, and tests the check with fixtures.                                           |
| `static-analysis-checklist/tracked-files-only`                     | Limits a CI guard's file discovery to tracked workflow and action files.                                           |
| `static-code-analysis/new-rust-service`                            | Picks the static checks for a new Rust service.                                                                    |
| `static-code-analysis/new-typescript-library`                      | Picks the static checks for a new TypeScript library.                                                              |
| `structured-logging/log-identifier-not-lookup`                     | Logs the customer id with a static message instead of looking up an email for the log.                             |
| `structured-logging/review-lookup-in-catch`                        | Logs the customer id with a static message instead of looking up the name in a catch.                              |
| `technical-writing/incident-writeup-from-notes`                    | Opens with a summary, separates support from engineering, flags unchecked claims, ends with the queries.           |
| `technical-writing/proposal-for-two-audiences`                     | Labels sections by audience, tables every flag and plan combination and the before and after, defines jargon.      |
| `technical-writing/review-runbook-wall-of-text`                    | Asks for the action first, steps as a list, jargon defined, and changing numbers replaced by how to fetch them.    |
| `test-authoring/dotnet-private-field-not-reflected`                | Proves a private dictionary was emptied through the public method, without reflection.                             |
| `test-authoring/fake-routes-by-request`                            | Has a fake HTTP client answer by request path, not by a queue of replies in call order.                            |
| `test-authoring/fixture-files-not-inline`                          | Reads saved fixture files instead of writing them in the test body.                                                |
| `test-authoring/review-direct-row-inserts`                         | Asks for an ordinary user to come from the factory, and accepts a direct insert for legacy data.                   |
| `test-authoring/review-helper-holds-assertions`                    | Flags an expected value recomputed with the production formula and assertions kept in a helper.                    |
| `triage-codex-security/approval-wave-cap`                          | Treats approval given before a wave was shown as not approving that wave.                                          |
| `triage-codex-security/repository-mismatch`                        | Stops when the findings belong to a different repository.                                                          |
| `typescript-authoring/batch-first-wrapper`                         | Syncs a list with one call per I/O step and makes the single-item function a wrapper of that call.                 |
| `typescript-authoring/rename-without-alias`                        | Updates every caller instead of keeping an alias, and adds the missing `await`.                                    |
| `typescript-authoring/unlink-batched-cache-delete`                 | Deletes cache keys with one `UNLINK` call, not `DEL` per key.                                                      |

## How to read the numbers

- **Three attempts is a small sample.** One failed attempt moves a case by a third of a grader's
  weight. Treat a single miss as something to re-run, not a finding.
- **The two score columns are not from the same run.** The default run has only the with-plugin
  arm. The no-plugin score was measured once per case, when the case was added or last replaced,
  and scores drift between days. Read the gap as "the model fails this without the skill", not
  as a precise difference. Six cases score 1.00 without the plugin:
  `bounded-iteration/review-dispatcher-predicate-and-job-id`, `review-ci-logs/quiet-log-keeps-errors`,
  `test-authoring/fake-routes-by-request` (0.00 when it was added), and the guard cases
  `feature-rollout/dark-route-without-caller`, `postgres-schema-design/checkout-exact-stock`, and
  `postgres-schema-design/review-advisory-lock-key`. Every other case scores below that.
- **A two-arm run leaves the loaded-the-skill grader out of both scores**, so the arms compare
  on the answer alone. `scripts/eval-summary.py` counts that grader again, so the "Score"
  column means the same thing from either kind of run.
- **The loaded-the-skill grader counts toward the score.** A good answer from an agent that did
  not load the skill scores below 1.00.

## What the suite does not measure

- **Whether the skill fires in a real session.** Each case runs in an empty sandbox with only this
  plugin installed. Other plugins and a real repository compete for the agent's attention.
- **Codex.** Every score is from Claude Code.
- **The effect of the command.** The publishing and triage cases grade the commands the agent
  attempts against synthetic fixtures. Nothing is published and no hosted service is written to.
- **Models other than the two named above.**

## Run it

```bash
pnpm run evals                  # every case
pnpm run evals npm-publishing   # one skill
python3 scripts/eval-summary.py # the table above, from evals/results/latest.json
```

The run needs Claude Code signed in, grants the agent `Bash`, and executes each case's
`fixture.sh` on your machine, so run it only on a checkout you trust. It does not run in CI. Raw
results land in `evals/results/`, which git ignores.
