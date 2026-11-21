# Kinds of eval for skills and agent instructions

A suite that tests skills, plugins, or agent instructions answers several separate questions. Run
each with the arms and attempt count it needs, and report each kind of result on its own.

| Kind             | Question                                                                   | Arms                                  | Attempts per case     |
| ---------------- | -------------------------------------------------------------------------- | ------------------------------------- | --------------------- |
| With vs. without | Does the thing under test change the answer? The top-line number.          | With and without                      | 3 or more per arm     |
| Loading          | Does the description make the skill load for the plain requests it should? | With only                             | 8 or more             |
| Description A/B  | Does a changed description load more often than the old one?               | Old and new description, same session | 8 or more per version |
| Neighbors        | When two skills overlap, does the right one load, or both when both apply? | With only                             | 8 or more             |
| Guard            | Does the skill stay out of the way where its rule does not hold?           | With and without                      | 3 or more per arm     |
| Regression       | Did a new model or harness version change loading or answers?              | With only, the full suite             | 3                     |

## With vs. without

- The difference between the arms is the result. Keep a case only when the without arm fails it;
  a case that passes without the thing under test shows only that it does no harm.
- Leave a loading grader out of the without arm's score; the skill cannot load there.

## Loading

- Every case carries a grader that checks the skill was called, but a loading question needs more
  attempts than an answer question: three attempts cannot tell a 70% load rate from a 90% one.
- Write the prompts the way people ask, without the skill's own wording, so they test the
  description and not recall of it.
- Keep traces for the misses and check whether the agent called no skill or another one. No call
  means the trigger is unclear; a neighbor's call means two descriptions need to divide the work.

## Description A/B

- Run the old and the new description back to back in one session, on the cases that miss. Keep
  the new one only if it loads more often. Record both counts.

## Neighbors

- Write a prompt that sits between two overlapping skills, and give it a loading grader for each
  skill that should load. Never fail a case because a neighbor also loaded; loading both is fine.

## Guard

- A guard case may pass without the thing under test. Keep one only beside a case for the same
  rule that fails without it, and label it a guard wherever scores are reported.

## Regression

- Rerun the suite after each model or harness upgrade, and record the versions with the result.
  Loading rates move with the harness even when no skill changed.

## From real failures

- When an agent gets something wrong in real work, first write a case that reproduces the
  mistake and check that it fails, then change the rule or description. The case measures the fix
  and catches the mistake if it returns.

## Retiring a rule

- When a case starts passing in the without arm, for example after a model upgrade, the rule it
  tests may no longer earn its place in context. Rerun it with more attempts and, if it still
  passes without the skill, consider deleting the rule rather than keeping it forever.
