# Evidence guide: where proof lives in a reproduction package

This is the rubric's map. For each proof family a check names, it says
where to look and what a passing instance looks like, in terms someone
else could apply without me in the room. "The report" is never a
source; a named line of the report read against a named line of the
issue is.

Package layout (eval bundle): `## Repo facts` (repo line, latest
release, "bug reports:" template asks, "contribution policy" line),
`## Issue` (title, opener, labels, body excerpt with the trigger and the
observed behavior), `## Thread highlights`, `## Candidate claim
comment`, `## Candidate repro report`. Live mode: the issue page and
thread, the repo's `CONTRIBUTING.md` / `AI_POLICY.md` / issue template,
and the student's draft files.

## Environment

**Where it lives.** Eval: the line or lines of the candidate repro
report that name what it ran on, usually opening with "Environment:";
the version and platform the Issue section states as the bug's target
(often in the opener line or the first paragraph); and the "bug
reports:" line under Repo facts, which lists what this repo's template
asks reporters to record. Live: the same lines in the draft
`repro.md`; the issue body's version/OS fields; the repo's
`.github/ISSUE_TEMPLATE` or bug-report template.

**What good looks like.** The report names the operating system and
the project version it ran, and any environment fact the issue or the
repo template treats as load-bearing for this bug: the driver on a
minikube driver issue, the shell on a prompt-rendering issue, the
install method where builds differ. The version named matches the
issue's target, or the report says in so many words that it differs
and that the behavior is still present. "Tested locally" with no
version, or a run on an older release with the delta unmentioned, is
an environment nobody can place.

## Steps

**Where it lives.** Eval: the numbered or prose steps of the candidate
repro report, read against the trigger the Issue section describes
(its commands, inputs, config, or user actions). Live: the steps in
the draft `repro.md`, read against the issue body's reproduction
section.

**What good looks like.** Every step can be executed by a stranger on
the thread who has the issue text and the recorded environment: exact
commands, exact inputs or files (shown inline, or identified precisely
enough in the issue that the report's reference to them leaves nothing
to guess), exact config contents, and a stated starting state. A step
fails this test when it needs something neither the report nor the
issue supplies: a private repository, an unshared config, a phrase
like "set up the project" or "my usual setup". Step count and formatting carry no weight; a single
copy-pasteable command block can be complete and a ten-step list can
be unfollowable.

## Behavior shown

**Where it lives.** Eval: the artifacts inside the candidate repro
report, meaning fenced output excerpts, log lines, exit codes,
screenshots described in text, and the report's "Actual:" line, all
read against the specific behavior the Issue section describes: its
exact error text, panic message, exit code, or visible symptom. Live:
the same artifacts in the draft, read against the issue body.

**What good looks like.** An artifact displays the issue's failure and
not a neighbor of it. Same error message or panic, same exit code,
same visible symptom. The common wrong-target shapes: a graceful
argument-validation error (exit 1) shown where the issue reports a
panic (exit 101); a compile error produced by a modified expression
instead of the issue's runtime error; garbled output with the program
still alive narrated as a crash; a version banner or session list that
proves the program runs but not that the bug fired. An honest
cannot-reproduce also passes here when its artifacts show a genuine
attempt at the issue's own trigger, the issue's commands or a faithful
re-creation, and the output of that attempt.

## Honesty

**Where it lives.** Eval: every sentence of result in the candidate
repro report ("reproduced", "confirmed", "crashes", "the cause is",
"could not reproduce", "on all my machines") read next to the
artifacts the same report shows. Live: the same in the draft
`repro.md`.

**What good looks like.** The words claim exactly what the artifacts
show and nothing more. A report says "reproduced" only when an
artifact shows the issue's behavior; names a cause only when an
artifact or a shown trace points at it; generalizes to other machines,
versions, or platforms only when those were run. An honest
cannot-reproduce says so in the first line, shows what was tried, and
names what differed from the issue's conditions and what a triggering
setup would likely need. Confidence language ("definitely",
"guaranteed reproducible", "as you can see") is not itself a fault; it
is a fault when the artifact underneath does not show the thing
asserted. Long and confident can be empty; terse and observational can
be complete.

## Comms

**Where it lives.** Eval: the candidate claim comment read against the
Issue section's title and body and the Thread highlights; both
comments read against the "contribution policy" line and the "bug
reports:" line under Repo facts. Live: the draft `claim.md` against
the issue page; both drafts against the repo's `CONTRIBUTING.md`,
`AI_POLICY.md` or equivalent, and its comment conventions.

**What good looks like.** A claim names something only this issue has,
its symptom, error, component, version, or a detail from the thread,
so it could not be pasted onto another issue, and it promises only
what its author controls: an investigation and a report, never a fix
or a date, never "assign me" or "reserve this for me". Boilerplate
("+1", "same here", "great project, kindly assign") fails on
specificity before it fails on anything else. For conventions, the
test is whether the repo's stated policy REQUIRES disclosure of AI
assistance: when it does, at least one comment says AI was used and
how; when the policy is silent, permits AI without a disclosure ask,
conditions AI use only on the human understanding the work, or scopes
its disclosure ask to pull requests, the comments pass on this point
whatever they say about AI. Course packages are treated as AI-assisted
work, so a requiring policy plus silent comments is the failure this
family exists to catch.
