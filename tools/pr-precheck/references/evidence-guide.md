# Evidence guide: where evidence lives in a PR package

This is the rubric's map. For each evidence family a check names, it says
where to look and what a passing instance looks like, in terms someone
else could apply. Almost every fact this week is a pairing: something the
PR shows or claims, read against something the plan or the repo stated
first. The plan is the fixed point; the PR is the claim.

Package layout (eval bundle): `## Repo facts` (repo line, latest release,
a "pull requests:" line with the template's sections, checklist items,
and required entries, and a "contribution policy" line), `## Issue`
(title, opener, labels, body with the reported behavior and its repro),
`## Thread highlights` (dated one-line summaries with each commenter's
association), `## Plan context` (the repro evidence the plan built on,
then the plan: cause, scope, not-in-scope, "Files:" line, test plan, any
deferral or deviation note), `## Candidate PR` (`### Title`,
`### Description`, `### Commits`, `### Diff` as a unified diff,
`### Test evidence`).

Live mode: the student's `plan.md` (sections Diagnosis, Scope, Files,
Approach, Test plan, Risks and unknowns, Deviations); the branch's diff
from `git diff main...HEAD` run in the working copy; the draft title and
description in the student's PR draft file; the captured test output the
student recorded before and after; the repo's `.github/PULL_REQUEST_TEMPLATE.md`
and `docs/CONTRIBUTING.md` (or `CONTRIBUTING.md`, `AI_POLICY.md`); and the
issue page and thread. A house-chain student reads the house plan and the
house repro pack where `plan.md` and the posted repro are named.

## Plan fidelity (harness category: silent-drift)

**Where it lives.** Eval: the Plan context's scope statement ("Scope, one
bounded change: ..."), its not-in-scope line, its "Files:" line, and any
line that defers or deviates ("deferred with a note", "not covered by
this change", "a maintainer decision left open"), read against the
`### Diff`: the `--- a/` / `+++ b/` headers give the file list, each
`@@` block is one hunk. The `### Description`'s claims about what changed
("implements exactly", "no changes beyond", "CHANGELOG entry added",
"docs now document") are read against the same hunks. Live: `plan.md`'s
Scope, Files, Approach, and Deviations sections against
`git diff main...HEAD`, and the draft description against that diff.

**What good looks like.** Every changed file is one the plan named, a
test for the planned change, or an entry the repo's template asks for;
every hunk does a thing the plan said or a thing a deviation note
records. Drift in the more-than direction shows up as a file the plan
never mentions, a new flag or setting, a helper rewritten "while in
there", or a renaming pass; the planned one-line fix being present and
right does not redeem it. Drift in the less-than direction shows up as a
deliverable the plan named (a test, a docs page, a second file) missing
from the diff with no note anywhere. An honest deviation re-ties a
mismatch only when it is written down in the plan or stated in the
description as recorded there; a commit message or code comment is not a
note. The description's fidelity claims are graded here, not under
comms: "no changes beyond the plan" over a diff that touches an unnamed
file is silent drift, and "the docs now document the limitation" over a
diff with no docs hunk is silent drift in the less-than direction.

## Test evidence (harness category: not-tested)

**Where it lives.** Eval: the `### Test evidence` section and any
before/after quoted in the `### Description`, read against the Plan
context's test plan: the step that re-runs the reported behavior's repro
(usually the issue's own command or script), the expected-after value it
states ("expect `1`", "expect exit 0", "expect both rows"), and each
suite or check command it names (`cargo test`, `pytest`, `go test`,
`zig build test`, `yarn test`, a `make` target). The Issue body says what
the reported behavior was, which is how to tell the repro from a control.
Live: the student's captured output before and after against the Test
plan section of `plan.md`.

**What good looks like.** The before state and the after state are both
written down as observed values, for the input the test plan maps to the
reported behavior: `0` then `1`; exit 101 then exit 0; `XFAIL` then
`PASSED`; the merged line then the preserved blank line; a dead prompt
then a toast. A before/after pair as two comment lines in one block is
fine; a transcript is fine; a described UI state at the named step is
fine. The after state matches the expected-after the plan wrote. Every
suite the test plan named is reported with its outcome ("`cargo test`
passes (612 tests)"). Not decisive: a run of the control only ("verified
on the single-file case" when the bug is the two-file case); a run of a
path the fix does not touch (a GET with header casing when the bug is a
single-header POST's Content-Type); a prose assurance ("colors work now",
"a full day with no timeouts"); a suite named in the plan and never
mentioned in the evidence. Where the issue lists two failure modes and
the plan's test plan names each, evidence for one of them is not
evidence for both.

## Diff quality (harness category: unreviewable)

**Where it lives.** Eval: the `### Diff`, one `@@` hunk at a time, and
the `### Commits` list as context for what the author thought they were
doing ("wip", "fmt + cleanup", "misc cleanups while debugging" are
tells, not grades). Live: `git diff main...HEAD` and `git log main..HEAD`.

**What good looks like.** The fix is visible and nothing else rides
along. The debris tells, each enough on its own: a commented-out line of
code (`// eprintln!(...)`, `// let mut buffered_lines = ...`, a "first
attempt, keeping for reference"); a debug print left in (`eprintln!("DEBUG
...")`, a `print` of a variable, a `log::debug!` the plan did not ask
for); a function, constant, or import nothing in the diff uses
(`_unused_buffer_probe`, `count_open_fds` behind `allow(dead_code)`); a
TODO added to an untouched line (`use std::collections::VecDeque; // TODO:
ring buffer?`); a hunk whose removed and added lines change no token
(re-indentation, re-wrapping, an import block regrouped to add one name,
a run of lines deleted and re-added identically). Lines that a real
change drags into a hunk as context are not churn: a hunk that changes
`clear=clear` to `clear=False` and re-prints the two lines around it is
an ordinary diff.

## Standards and comms (harness category: standards-wall)

**Where it lives.** Eval: the Repo facts' "pull requests:" line (the
template's sections, its checklist items, and any required entry: a
`closes #` line, a changelog or whatsnew entry, a conventional-commit
title) and its "contribution policy" line (whether AI disclosure is
required), read against the `### Description` for sections and lines and
the `### Diff` for file entries. The Thread highlights' comments by an
OWNER, MEMBER, or COLLABORATOR, or by the issue's author, read against the
description and the plan, live here too. Live: `.github/PULL_REQUEST_TEMPLATE.md`
and `docs/CONTRIBUTING.md` (or `CONTRIBUTING.md`, `AI_POLICY.md`) against
the draft description and the branch's diff; the issue page's thread
against the draft description and `plan.md`.

**What good looks like.** Each template section is present with content
about this PR (a "Problem" and a "Solution"; a "Description" and
"User-facing changes"; a checklist with each item addressed or marked
n/a with a reason). Each required entry is where the template puts it:
`closes #66657` in the description, the whatsnew `.rst` entry in the
diff, the CHANGELOG line in the diff. A wall is visible: a required
checklist nowhere in the description, a required entry nowhere in the
diff. For disclosure, the question is whether the policy REQUIRES it:
"all AI usage in any form must be disclosed" requires, and a description
with no disclosure fails; "AI-assisted coding is welcome with a human in
the loop; comments must be in your own words" does not require, and the
check passes on that point whatever the description says. Course packages
are treated as AI-assisted work. Asks a PR package cannot show (a CLA, a
vouch flow, CI green) are not graded. For direction, a compliant
description names what the maintainer said (the pointer to
`IslandWindow.cpp`, the "no private APIs" constraint, fdncred's
uniqueness question) and follows it or says why not; where no maintainer
or author gave direction, there is nothing to engage.
