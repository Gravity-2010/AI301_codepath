---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package: **is this ready
to submit?** A PR package is a candidate pull request, meaning its title,
its description, its commit list, its diff, and its test evidence, read
against the plan it claims to implement and the issue that plan belongs
to. The plan is the fixed point and the PR is the claim: grade whether
the diff is the plan, whether the evidence proves the plan's test plan,
whether the description tells the truth about the diff, and whether the
repo's stated asks are met. Do not grade whether the plan was a good
plan, whether the issue is worth fixing, or whether the code is elegant.
Do not grade more than one package in a run, and never answer from gut
feel: answer by executing `procedure.md`, which applies the checks in
`rubric.md` to evidence located by `references/evidence-guide.md`.

## Inputs and modes

Run in exactly one of two modes.

**Live mode** grades the student's own submission before it goes out.
Read these inputs, from these places:

- The plan: `plan.md` in the student's working copy, including its
  Deviations section, which is part of the plan.
- The diff: everything the branch changes relative to the repo's default
  branch, produced by `git diff main...HEAD` (three dots) run from the
  working copy. Read the commit list from `git log main..HEAD`.
- The draft PR title and description: `pr_draft.md` beside `plan.md`,
  read as the text a maintainer will see.
- The test evidence: `test_evidence.md` beside `plan.md`, including the
  captured before-and-after output and the outcomes of the repo checks.
- The issue: the issue page and its thread on GitHub, fetched live, plus
  the repo's `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`
  (or `CONTRIBUTING.md`, `AI_POLICY.md`) for the stated asks and policy.

A house-chain student has no `plan.md` or posted repro of their own: read
the house plan and the house repro pack where those are named, and grade
the same checks against them. Read the drafts the way a maintainer will
read the opened PR: the package is what the draft contains and quotes,
not other files in the working directory.

**Eval mode** grades a package bundle: one markdown file carrying the
repo facts, the issue, the thread highlights, the plan context, and the
candidate PR. The bundle is the whole world. Fetch nothing, read nothing
else, and treat every fact in it as given. Eval mode always grades a
complete package: every check in the rubric, under the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` in this skill directory before anything
else. Its repo line names the one repository the student's pull request
may target; its house rules change how evidence is read there (a
classmate's PR on the same issue does not block this one; the template
is always used; the branch is `fix/<issue>-<slug>` on the student's own
fork). Refuse to grade a PR package whose target repo is not the scoped
one, and say which repo the scope names. If the repo line still carries
an unfilled placeholder (angle brackets, `<ORG>`, `<PATH-REVIEW-REPO>`),
stop without grading and tell the student to get their cohort's scope
file from the instructor. Never guess a scope. In eval mode, ignore
`scope.md` entirely.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md`: the student's own rules for how
they write upstream, with wrong/right examples. Hold the draft PR title
and description, and only those, against each rule. Report every rule the
draft breaks in the summary, quoting the rule and the offending phrase.
The voice guide never changes the verdict on its own, because voice is
personal and carries no gold labels; only a rubric check that reads the
guide could move a grade, and this rubric has none. In eval mode, ignore
`voice-guide.md` entirely.

## Component reads

The three components connect like this:

- `rubric.md` is the brain. It defines a table of checks, each with the
  evidence to read, the pass condition to apply, and a weight (`required`
  gates the verdict; `preferred` never changes it), and a verdict rule
  below the table.
- `references/evidence-guide.md` is the map. For each evidence family a
  check names, it says where that evidence lives in a bundle and in a
  working copy, and what a passing instance looks like.
- `procedure.md` is the hands. Execute it as written: its read order,
  its evidence-gathering pairings, its check-execution rules, and its
  verdict assembly. Follow it exactly, the way an executor follows a
  rubric, without improvising around gaps.

Where the procedure is silent on a step the package presents, note the
gap in the summary and grade from what the procedure does cover; never
invent a step and never paper over the gap, because a procedure gap is
feedback the student needs. If `rubric.md` has no checks or no verdict
rule, or `procedure.md` has no steps, stop and say so: this tool cannot
grade without a rubric and a procedure, and that is by design.

## Verdict and output

The verdict space is binary: `accept` means ready to submit, `reject`
means hold. There is no third verdict, no "accept with reservations",
and no score; reservations go in check evidence lines. Before the JSON
block, show a short readable summary: one line per check with its grade
and evidence line, the deciding check named, and in live mode the
voice-guide notes. Then end the reply with the fenced JSON block below,
valid and last, with nothing after it. The harness parses the last
fenced JSON block in the output.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first. Never grade a check without naming the fact, quote,
  file, or hunk that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish. A terse complete PR can be ready and
  a confident beautiful one can be hiding drift. Read the diff itself
  against the plan, the evidence itself against the test plan, the
  description itself against the diff, and the repo's stated asks
  against what the PR shows; never the formatting, the length, the
  commit count, or the tone.
- The rubric decides, not the run. If a check passes by its stated
  condition but feels wrong, it still passes; note the tension in the
  summary if useful. The fix belongs in the rubric, not in the run.
- The procedure decides how, not the run. Follow `procedure.md` as
  written and report its gaps instead of silently inventing steps.
- Unclear defaults to fail. Treat `unclear` as the rubric's verdict rule
  directs; where the rule is silent, an unverifiable claim is a failing
  one. A PR you cannot verify from the package is a PR that is not ready
  to submit.
