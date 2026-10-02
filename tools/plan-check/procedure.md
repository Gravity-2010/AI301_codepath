# Procedure: how this skill grades a plan package

Follow these steps as written. Where a step is silent on something the
package presents, say so in the summary rather than inventing a step.

## Read order

Read the package in this order, and write one short note per part
before moving to the next. The order puts every piece of evidence in
hand before the plan is read, so the plan is read as a claim to be
checked against notes already taken, never the other way round.

1. **Repo facts.** Note the latest release, what the bug-report
   template asks for, and the contribution policy. For the policy,
   write exactly one of: `requires AI disclosure`, `conditional (no
   disclosure requirement)`, or `silent on AI`. Quote the policy
   phrase that decided it.
2. **Issue.** Note the reported behavior (the exact error, output, or
   symptom), the trigger that produces it, and any cause the reporter
   proposes. Mark a reporter's cause as `proposed, unverified`.
3. **Thread highlights.** For every comment by an OWNER, MEMBER, or
   COLLABORATOR, or by the issue's author, note any direction it
   gives: an isolated culprit or file, a chosen or rejected approach,
   a posted test build or pull request, a request to test or report.
   Write each as one line beginning `direction:`. If there is none,
   write `no maintainer direction`. Note other commenters' claims as
   `proposed, unverified`.
4. **Repro evidence.** Note the artifact (quote the error or output),
   each control run and what it rules in or out (one line per
   control: `control: <what changed> -> <what happened> -> rules
   out/in <what>`), and the Expected and Actual lines. This is read
   before the plan on purpose: the diagnosis check compares the plan's
   cause to these notes, and a cause read first tends to make the
   evidence look like it fits.
5. **Candidate plan.** Note: the stated cause (quote it); every
   in-scope item; every not-in-scope item and its stated reason; every
   file, module, or function named; each approach step; each test and
   the observable it names; each risk or unknown. Where the plan has
   no statement for one of these, write `none stated`.
6. **Candidate plan comment.** Note what it names from the issue or
   thread, what it promises, whether it mentions AI assistance, and
   whether it engages each `direction:` line from step 3.

Live mode: the issue page and its thread stand in for steps 2 and 3;
the student's posted repro comment on that issue is the repro evidence
for step 4 (for a house issue, the repro pack as quoted in the drafts);
the repo's `CONTRIBUTING.md`, `AI_POLICY.md` or equivalent, and issue
template stand in for step 1; `plan.md` and the draft comment are steps
5 and 6. If the drafts quote no repro evidence and there is no posted
repro comment, write `no repro evidence` at step 4 and let the checks
that need it grade from that absence.

## Evidence gathering

For each evidence family the rubric names, the gathering move is a
lookup into the read-order notes. No check sends the executor back to
hunt through the package.

- **Diagnosis and grounding.** Take the plan's cause (note 5) and lay
  it against the artifact and each `control:` line (note 4). For each
  control, record `consistent` or `ruled out`, with the reason. If a
  thread or reporter cause (notes 2 and 3) matches the plan's cause,
  record that the plan adopted it, and still test it against the
  controls.
- **Scope.** Take every in-scope item and approach step (note 5) and
  classify each one as `fixes the isolated behavior`, `stated deferral
  with reason`, or `not asked for`. The test for `not asked for` is
  the issue body and the repro's Expected line (notes 2 and 4), not
  whether the work sounds useful. Record any "while in the area" or
  "while touching" phrasing verbatim.
- **Executability.** From note 5, record the files or modules named
  (or `none`), the approach (`one chosen`, `several left open`, or
  `none`), and quote any deferred decision ("whichever is easier",
  "investigate and then decide", a list of candidates with no pick).
- **Test plan.** For each test in note 5, record: the observable it
  names (or `none`), the repro step or artifact it maps to (or
  `none`), the expected post-fix value (or `none`), and whether it
  would pass before the fix.
- **Honesty.** From note 5, record each risk or unknown and what the
  plan says it will do about it. Record any decision the plan states
  as settled that the repro evidence (note 4) leaves open.
- **Comms.** Take each `direction:` line (note 3) and record whether
  the plan or comment names it and follows it or departs with a
  reason. Take the policy classification (note 1) and record whether
  the plan or comment discloses AI assistance.

## Check execution

1. Execute the checks in the rubric's table order: Diagnosis grounded
   in the evidence, One bounded change, A stranger could start,
   Decisive test plan, Engages the thread, Repo conventions and AI
   disclosure, then Risks and unknowns named. Diagnosis runs first
   because a cause the evidence rules out makes the scope and test
   judgments about the wrong fix, but every check is still graded and
   reported.
2. Grade each check from the gathered records only, by applying the
   rubric's pass condition to them. Do not re-read the package to find
   something the records missed; if a record is missing, the read
   order was not followed, so go back to the read order, not to the
   check.
3. For each grade, write one evidence line: the quoted phrase or
   recorded fact that decided it. "Looks fine" is not an evidence
   line.
4. When the evidence a check needs is genuinely absent from the
   package (the plan states no cause, names no files, gives no test,
   the thread has no highlights), grade `unclear` and write the
   absence as the evidence line ("no cause stated in the plan"). Do
   not infer a cause, a file, or a test from what the plan would
   probably mean.
5. When a pass condition turns on whether the issue "asked for"
   something, decide from the issue body and the repro's Expected line
   alone. When it turns on whether a control "rules out" a cause,
   decide from what the control changed and what happened, as
   recorded. Plausibility, polish, length, and section headings never
   enter a grade.
6. Two executors with the same records must reach the same grade. If
   a grade feels like a judgment call, write down which recorded fact
   it turns on and grade from that fact.

## Verdict assembly

1. If every `required` check is `pass`, the verdict is `accept`.
2. If any `required` check is `fail` or `unclear`, the verdict is
   `reject`. `unclear` enters the rule as `fail`.
3. The `preferred` check is reported with its grade and never enters
   the rule.
4. The deciding check is the first `required` check in table order
   that is not `pass`. Quote its evidence line in the summary as the
   reason for the hold. On an `accept`, quote the fix-specific
   observable from the test plan as the thing the build can be held
   to.
5. Write the summary as one line per check (name, grade, evidence
   line), then, in live mode, the voice-guide notes: each voice rule
   the draft comment breaks, with the rule quoted. Then emit the JSON
   block last, with one entry per check in table order and the
   verdict. Nothing follows the JSON block.
6. After a recorded deviation (live mode), run this whole procedure
   again on the updated `plan.md`, reading its Deviations section as
   part of the plan for the first four checks.
