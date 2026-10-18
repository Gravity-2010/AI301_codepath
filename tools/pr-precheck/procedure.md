# Procedure: how this tool grades a PR package

Follow these steps as written. Where a step is silent on something the
package presents, say so in the summary rather than inventing a step.

## Read order

Read the package in this order and write the notes named under each
part before moving on. The plan is read before the diff so that the diff
is read as a claim against a scope already written down; the diff is
read before the description so that the description's claims are checked
against hunks already classified, never the other way round (a confident
"no changes beyond the plan" read first makes an unplanned hunk look
planned).

1. **Repo facts.** From the "pull requests:" line, list every template
   section, checklist item, and required entry as one line each,
   beginning `ask:` (for example `ask: closes #xxxx line`, `ask: whatsnew
   entry in diff`, `ask: Problem section`). Write `no template` if the
   line says so, and still list any stated ask from the contribution
   docs ("tests with behavior changes", "CHANGELOG.md entry"). From the
   "contribution policy" line write exactly one of: `requires AI
   disclosure`, `conditional (no disclosure requirement)`, or `silent on
   AI`, and quote the phrase that decided it.
2. **Issue.** Note the reported behavior (the exact output, error, or
   symptom), the input or command that produces it, and, where the issue
   lists more than one failure mode, each one by number.
3. **Thread highlights.** For every comment by an OWNER, MEMBER, or
   COLLABORATOR, or by the issue's author, that gives direction (a
   culprit or file, a chosen or rejected approach, a constraint, a test
   build, an open question put to the fixer), write one line beginning
   `direction:`. If there is none, write `no maintainer direction`.
4. **Plan context.** Note, quoting where possible: the scope statement;
   the not-in-scope line; every entry on the "Files:" line; every
   deliverable the plan names (code change, each test, docs, changelog);
   every deferral or deviation line (`deferred:` one per line); and the
   test plan as one line per step, each with the input it runs, the
   expected-after value it states, and whether it is the reported
   behavior's repro, a control, or a suite or check command. Mark the
   step that re-runs the reported behavior `repro step`.
5. **Diff.** Write the file list from the `--- a/` headers. Then write a
   hunk ledger: one line per `@@` block, naming the file and what the
   hunk does in a few words, and any debris tell it carries (commented-out
   code, debug print, unused function or import, added TODO, no-token
   change). Do this before classifying anything against the plan.
6. **Commits.** Note the messages as context only (`wip`, `fmt`, `misc
   cleanups` are tells to remember when reading the ledger, not grades).
7. **Title and description.** List every claim about what the PR
   changes, adds, covers, or leaves alone, one per line beginning
   `claim:`; mark any fidelity claim (`exactly`, `no changes beyond`,
   `no functional changes outside`) as `fidelity claim:`. Note which
   template sections and checklist items from step 1 appear, and whether
   the description discloses AI assistance and to what extent.
8. **Test evidence.** List each command or input shown, what it printed
   before and after (or `before not shown` / `after not shown`), and each
   suite or check reported with its outcome (or `not reported`).

Live mode: `plan.md` is step 4 (read its Deviations section as part of
the plan); `git diff main...HEAD` is step 5 and `git log main..HEAD` is
step 6; the draft title and description are step 7; the student's
captured before/after output is step 8; the repo's PR template and
contributing guide are step 1; the issue page and thread are steps 2 and
3. A house-chain student's house plan and house repro pack stand in for
`plan.md` and the posted repro.

## Evidence gathering

For each evidence family the rubric names, the gathering move is a
pairing of two sets of notes already taken. No check sends the executor
back to hunt through the package.

- **Plan fidelity, more-than direction.** Take each ledger line (note 5)
  and classify it against notes 4 and 1 as exactly one of: `planned`
  (implements a stated step in a named file), `test for planned change`,
  `repo-asked entry` (a changelog, whatsnew, or release-notes file an
  `ask:` line names), `recorded deviation` (a `deferred:` or deviation
  line in the plan, or a description line that says the plan records
  it, covers it), or `unplanned`. Record every `unplanned` line verbatim.
- **Plan fidelity, less-than direction.** Take each deliverable from
  note 4 and mark it `present` (a ledger line delivers it), `recorded
  absent` (a `deferred:` line or a description sentence records the
  shortfall), or `silently absent`.
- **Description against diff.** Take each `claim:` line (note 7) and
  mark it `supported` (a ledger line contains it) or `unsupported`. Mark
  each `fidelity claim:` as `holds` or `contradicted` from the two
  fidelity records above.
- **Test evidence.** Take the `repro step` line(s) from note 4 and find
  the evidence line (note 8) that runs the same input; record `before
  shown`/`not shown`, `after shown`/`not shown`, `after matches
  expected-after`/`does not`, or `repro step not run` when every shown
  input is a control or a different path. Take each suite or check line
  from the test plan and record `reported: <outcome>` or `not reported`.
- **Diff quality.** Take every debris tell from the ledger and list them;
  for a no-token hunk, confirm from the ledger that removed and added
  lines differ only in whitespace, ordering, or grouping, and that no new
  name is introduced by a single line that would have sufficed alone.
- **Standards.** Take each `ask:` line (note 1) and record `met in
  description`, `met in diff`, `marked n/a with reason`, `not visible in
  a package`, or `ignored`. Take the policy classification and record
  `disclosure present`/`absent` from note 7.
- **Direction.** Take each `direction:` line (note 3) and record whether
  the description or plan names it and follows it or departs with a
  reason.

## Check execution

1. Execute the checks in the rubric's table order: Diff inside the plan,
   Plan delivered or shortfall recorded, Description matches the diff,
   Decisive test evidence, Reviewable diff, Repo asks and AI disclosure,
   then Maintainer direction engaged. Fidelity runs first because a diff
   that is not the plan makes the evidence and standards judgments about
   the wrong change, but every check is still graded and reported.
2. Grade each check from the gathered records only, by applying the
   rubric's pass condition to them. Do not re-read the package to find
   something the records missed; if a record is missing, the read order
   was not followed, so go back to the read order, not to the check.
3. For each grade, write one evidence line: the quoted phrase, file,
   hunk, or recorded fact that decided it. "Looks fine" is not an
   evidence line.
4. When the evidence a check needs is genuinely absent from the package
   (no test-evidence section, no plan scope, an empty diff), grade
   `unclear` and write the absence as the evidence line ("no test
   evidence provided"). Do not infer that a test was run, a file was
   planned, or a section was meant.
5. When a pass condition turns on whether something is "named by the
   plan", decide from the quoted scope, "Files:" line, and deliverable
   notes alone. When it turns on whether evidence is "the repro", decide
   from the input shown against the `repro step`'s input. When it turns
   on "requires", decide from the quoted policy phrase. Plausibility,
   polish, length, commit count, and section headings never enter a
   grade.
6. Two executors with the same records must reach the same grade. If a
   grade feels like a judgment call, write down which recorded fact it
   turns on and grade from that fact.

## Verdict assembly

1. If every `required` check is `pass`, the verdict is `accept`.
2. If any `required` check is `fail` or `unclear`, the verdict is
   `reject`. `unclear` enters the rule as `fail`.
3. The `preferred` check is reported with its grade and never enters the
   rule.
4. The deciding check is the first `required` check in table order that
   is not `pass`. Quote its evidence line in the summary as the reason
   for the hold. On an `accept`, quote the before-and-after observable
   from the test evidence as the thing a reviewer can hold the PR to.
5. Write the summary as one line per check (name, grade, evidence line),
   then, in live mode, the voice-guide notes: each voice rule the draft
   title or description breaks, with the rule quoted. Then emit the JSON
   block last, with one entry per check in table order and the verdict.
   Nothing follows the JSON block.
6. In live mode, after any fix to the branch, the plan's Deviations
   section, or the draft description, run this whole procedure again
   before the PR is opened. A PR opened without a passing run of this
   procedure on its final draft was never checked.
