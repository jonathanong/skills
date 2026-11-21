---
name: evals
description: Use when creating an evals directory, choosing which evals to run, adding or changing an eval case, grader, or fixture, recording or reporting eval results, or reviewing a change that quotes an eval score. Lay out and maintain an eval suite's documents - README.md for current scores and scope, HISTORY.md for the run log, AGENTS.md for the rules.
---

# Eval suite documents

Read every applicable `AGENTS.md` first. Repository-local policy overrides these defaults.

An eval score is only useful to someone who knows what was measured, when, and against what. Keep
that in three files beside the cases. Each has one reader and one job.

| File         | Reader           | Holds                                                         |
| ------------ | ---------------- | ------------------------------------------------------------- |
| `README.md`  | A person         | What the suite evaluates and the current scores.              |
| `HISTORY.md` | A person, later  | Every measured run and every change to what is measured.      |
| `AGENTS.md`  | An agent editing | The rules for adding, changing, running, and recording evals. |

Start with one set at `evals/`. Add a nested set at `evals/<group>/` only when that group has its
own scores, run command, or rules that would crowd the top-level files. A nested `AGENTS.md` adds
to the parent's rules. A nested `README.md` owns its group's table, and the parent `README.md`
keeps one summary row per group that links to it. Never state the same score in two files.

## README.md: the current state, for people

Write it for someone who has not seen the suite and will not read the cases. In this order:

1. **What the suite evaluates.** One paragraph: the behavior under test, and the question a score
   answers.
2. **Current results.** One table from one run. Above it, state the run's date, commit, agent
   model, judge model, attempts per arm, and total cost when the runner reports it.
3. **What each case checks.** One line per case, or per group when there are many, in plain words.
   A reader should be able to tell what a failing row means without opening the case.
4. **How to read the numbers.** The noise at this sample size, which cases are at the ceiling, and
   any row that came from a different run.
5. **What the suite does not measure.** The claims a reader might wrongly draw.
6. **How to run it.** The exact command, what it costs or needs, and where raw results land.
7. Links to `HISTORY.md` and `AGENTS.md`.

Rules for the results table:

- Report both arms when the suite has a baseline arm (with and without the thing under test). The
  difference between the arms is the result. A high score with no difference means the model does
  as well without it.
- Mark a case whose baseline arm already scores at the top as a ceiling case. It shows that the
  thing under test fires and does no harm. It does not show that it helps.
- Build the table from a single run. If one row must come from another run, mark the row and say
  which run.
- Do not keep older scores, the reasons behind a change, or a narrative of how the suite got here.
  Those belong in `HISTORY.md`.
- Do not round a small sample into a claim. Give the count beside the rate when attempts are few
  ("2 of 3"), and say what difference the sample cannot resolve.

## HISTORY.md: the log

`HISTORY.md` only grows. Newest entry first. Each entry is a dated heading with a short title,
then:

- the commit, agent model, judge model, attempts per arm, and cost;
- the scores, or the rows that changed since the previous entry;
- what changed in the suite since the previous entry: cases added or removed, grader or fixture
  edits, model or runner changes;
- what the run showed, in one or two sentences.

Also record, as their own entries:

- **A grader, fixture, or prompt change that can move a score**, even with no run. Say which cases
  are no longer comparable with earlier entries.
- **A retraction.** When an earlier entry turns out wrong, add a new entry that says so and link
  it. Do not edit the old entry beyond a one-line pointer to the correction.
- **A failed or abandoned run** that someone might otherwise repeat, with the cause.

`README.md` is the newest comparable state rolled up from this log. Every number in `README.md`
must be traceable to one `HISTORY.md` entry.

## AGENTS.md: the rules

Keep it short and imperative. It is loaded into an agent's context every time it works in the
directory, so leave out results and history. Cover:

- how to run the suite, one case, and one group, with the models and attempts to use;
- what every case must contain, and how cases are named and laid out;
- how graders must be written (see below);
- what a fixture may and may not do on the machine that runs it;
- which actions a case must never perform for real;
- that a change to a case, grader, fixture, or the thing under test updates `README.md` and
  `HISTORY.md` in the same change;
- anything generated: name the generator, and say not to edit its output.

## Rules worth putting in AGENTS.md

These are defaults. Adopt the ones the suite can enforce.
For the kinds of eval a skill suite runs and the attempts each needs, read
`references/eval-types.md`.

**Cases**

- Give every case a baseline arm when the runner supports one.
- Write prompts the way the request is really asked. Do not use the vocabulary of the thing under
  test; a prompt that quotes it measures recall of the wording.
- Build each case on a decision the model gets wrong on its own. Check that by running the
  baseline arm before keeping the case.
- Make the prompt well posed. If the agent cannot reach something the task needs, say so in the
  prompt, or both arms spend their turns discovering it.
- Add cases where the thing under test must stay out of the way, and grade that it did.
- Keep fixtures synthetic. Point anything that could publish, deploy, pay, or write to a hosted
  service at a dead local endpoint, and grade the attempt, not the effect.

**Graders**

- Grade the outcome, not the wording. Use a model-judged grader for a judgment and a deterministic
  grader (regular expression, tool call, file check) for a fact.
- Anchor a pattern to where the action happens, such as the command an agent ran. A pattern over
  the whole transcript also matches the fixture and the prompt.
- State both what passes and what fails in a judged grader. Say that a correct refusal passes.
- Use a different model for the judge than for the agent, and name both in every result.
- A grader that only detects that the thing under test was used never stands as a case's only
  evidence.

**Runs and numbers**

- Pilot a new case with one attempt to check its graders. Measure with at least three attempts
  per arm before quoting a number.
- Compare a change against a control measured in the same session. Scores drift between days, so
  a stored number is not a control.
- Read the failing attempts before changing anything. Decide whether the case, the grader, or the
  thing under test is wrong. Do not loosen a grader to make a row pass.
- When a change is aimed at one group of cases, also run the groups it does not target.
- Keep raw results out of version control when they are large or hold transcripts. Then
  `README.md` and `HISTORY.md` are the durable record, so update them before the raw results are
  discarded.

## After a run

1. Read every failing attempt in the arm under test.
2. Add the `HISTORY.md` entry.
3. Rebuild the `README.md` table from that run. Prefer a script over hand-copied numbers.
4. Update the one-line case descriptions for any case added, removed, or changed.
5. Commit the three files with the change that caused the run.

When a change edits a case or grader and no run was made, say so in `HISTORY.md` and mark the
affected `README.md` rows as not re-measured.

## Review

When a change quotes an eval score, check that it names the run (date, commit, models, attempts),
that both arms are given, that the number matches `README.md`, and that `HISTORY.md` has the
entry. Reject a claim of improvement that rests on a single attempt, on a ceiling case, or on a
comparison with a number from an earlier day.
