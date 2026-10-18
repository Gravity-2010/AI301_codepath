# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

[Link to the pull request you opened. It must be the pull request's own page on the Path
Review repo, not your fork's branch page.]

**Branch**

[The name of the branch the pull request comes from, exactly as it appears in your fork.
The naming shape is a type prefix, then the issue number, then a short description. **The
issue number in the branch name must be the number of the issue the pull request fixes** —
a name carrying any other number does not satisfy this field.]

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

2026-10-18 — Attempted one full 20-package run with Sonnet using
`run_eval.py --skill ... --save-run ...`. All 20 package evaluations
errored (`claude exited 1` after the harness retry),
so the harness produced no agreement score and correctly did not write
`eval-run.txt`. The direct CLI error was: “You've hit your org's monthly
spend limit · ask your admin to raise it at claude.ai/admin-settings/usage”.
A successful full run is still required after the organization limit resets.

**Package analysis**

`pkg-01` (gold: `reject`). A manual application of my rubric also rejects
it at **Repo asks and AI disclosure**: pandas' repo facts require a
`closes #xxxx` line, checklist items, and a `whatsnew` entry, while the
description has no closes line or checklist and the diff has no
`whatsnew` file. The implementation and test evidence are otherwise
strong, but the required standards check holds the package. This is a
manual rubric reading, not a harness verdict; the full eval is blocked
by the spend limit above.

**Check rationale**

> Repo asks and AI disclosure | The Repo facts' "pull requests:" line (the template's sections, checklist items, required entries such as a closes line, a changelog or whatsnew entry) and its "contribution policy" line, read against the Candidate PR's description and diff | Every section or checklist item the template names appears in the description with content specific to this PR, or is marked not applicable with a reason; every required entry appears where the template puts it (a closes or fixes line in the description; a changelog, whatsnew, or release-notes file in the diff). If the stated policy REQUIRES disclosing AI assistance ("must be disclosed", "all AI usage must be stated"), the description states that AI assistance was used and to what extent; a policy that is silent on AI, welcomes it with a human in the loop, or asks only for the contributor's own words does not require disclosure, and the check passes on that point whatever the description says. Where there is no template and no required entry, that part passes. Asks whose satisfaction is not visible in a PR package (a signed CLA, a vouch flow, CI status, a maintainer discussion before the PR) are not graded. Course packages are treated as AI-assisted work | required

I kept this as a required check because the rubric has to catch the
standards-wall packages, including required template entries and actual
AI-disclosure policies. Its conditional policy language avoids treating
AI disclosure as mandatory when a repo is silent or merely welcomes
human-reviewed AI use.

**Trade-offs**

The standards check intentionally ignores asks that cannot be verified
from the package (such as CI status or a signed CLA), so it may not catch
every repository process requirement. In exchange, it avoids inventing
evidence that is not present in the frozen package and keeps the decision
focused on visible PR requirements. This trade-off is specified in the
quoted check; it has not yet been tested by a successful eval run.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
