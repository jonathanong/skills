# Eval rules

The suite runs with `claude plugin eval`. `README.md` in this directory holds the current scores
and `HISTORY.md` holds the log. The `evals` skill in this plugin describes how the three files
work; follow it.

## Run

```bash
pnpm run evals                  # every case, with the plugin
pnpm run evals <skill> ...      # the cases tagged with those skills
EVAL_FLAGS='--runs 1' pnpm run evals <skill>              # pilot a new case
EVAL_ABLATION=with-without pnpm run evals <skill>         # add a no-plugin arm
python3 scripts/eval-summary.py                           # the table for README.md
```

- Sonnet is the agent and Opus is the judge. Measure with three attempts, the default.
- The default run has no no-plugin arm. Run that arm once for a new case, to check that the model
  does not already pass it without the skill, and record what it scored in `HISTORY.md`.
- A two-arm run leaves `skill-fired` out of its own score. `scripts/eval-summary.py` counts it
  again; use that script's numbers, not the report's.
- The run needs model credentials and executes each case's `fixture.sh` on the machine. Run it
  only on a checkout you trust. It does not run in CI.

## Cases

- Layout: `evals/<skill>/<case>/prompt.md`, `graders/*.md`, and an optional `fixture.sh`.
- Tag every case with its skill name.
- Every case has `graders/skill-fired.md`: a `tool_used` grader on the `Skill` tool that names the
  skill. `pnpm run evals:check` fails a skill with no such case.
- Every case has at least one more grader that checks the outcome. Skill-fired alone proves
  nothing about the answer.
- When the work also needs a companion skill that the skill names, a case may add a second
  `tool_used` grader for it. Do not add a `max: 0` grader against a neighboring skill; loading
  both is fine.
- Write the prompt as a person would ask it. Do not use the skill's own wording, and do not name
  the skill.
- Build a case on a decision the model gets wrong without the skill.
- The exception is a guard case: one that checks a rule is not applied where it does not hold.
  It may pass without the skill. Add one only beside a case for the same rule that the model
  fails without the skill, and call it a guard in `README.md` and `HISTORY.md`.
- Keep prompts and fixtures free of the names of real organizations and of private repositories
  and products. Naming a public library or tool the case is about is fine.

## Graders

- Use an `llm` grader for a judgment. State what passes and what fails.
- Use a `regex` grader for a fact. Anchor a pattern about a command to the Bash `"command"` input;
  a pattern over the whole transcript also matches the prompt and the fixture.
- Grade the rule the case is named for. A grader that also requires rules from other parts of
  the skill fails good answers and hides which rule was missed.
- Do not loosen a grader to make an attempt pass. Read the trace first and decide whether the
  case, the grader, or the skill is wrong.

## Actions a case must never perform

- No real `npm publish` and no mutating `npm trust`. Fixtures point at a dead local registry, and
  the graders check the commands the agent attempts.
- No write to a hosted service: no issue, comment, label, or pull request.
- No reading of credentials: never `cat ~/.npmrc` and never dump the environment.

## Kinds of run

The `evals` skill's `references/eval-types.md` defines each kind. Here:

| Kind             | Command                                                                  |
| ---------------- | ------------------------------------------------------------------------ |
| With vs. without | `EVAL_ABLATION=with-without pnpm run evals <skill>`                      |
| Loading          | `EVAL_FLAGS='--runs 8 --case <case> --keep-temp' pnpm run evals <skill>` |
| Description A/B  | The loading command once per description version, in the same session    |
| Neighbors        | A case with a `tool_used` grader for each skill that should load         |
| Guard            | The with-vs-without command; label the case a guard                      |
| Regression       | `pnpm run evals` after a Claude Code or model upgrade                    |

- Before changing a description to make a skill load, run the A/B on the cases that miss and keep
  the change only if it loads more often.
- `--keep-temp` keeps the traces. Check whether a miss called no skill or another one. No call
  needs a clearer trigger; a neighbor's call needs the two descriptions to divide the work.

## Record

- After a measured run, add an entry at the top of `HISTORY.md` and rebuild the table in
  `README.md` with `scripts/eval-summary.py`.
- A change to a case, grader, or fixture with no run still gets a `HISTORY.md` entry. Mark the
  affected rows in `README.md` as not re-measured.
- `evals/results/` is ignored by git, so these two files are the only durable record.
