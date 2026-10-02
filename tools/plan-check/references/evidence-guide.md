# Evidence guide: where evidence lives in a plan package

This is the rubric's map. For each evidence family a check names, it
says where to look and what a passing instance looks like, in terms
someone else could apply. A plan's evidence lives in different places
from a repro's: most of it is a claim in the plan read against a fact
in the repro-evidence block or the thread.

Package layout (eval bundle): `## Repo facts` (repo line, latest
release, "bug reports:" template asks, "contribution policy" line),
`## Issue` (title, opener, labels, body excerpt with trigger and
observed behavior), `## Thread highlights` (dated one-line summaries
with each commenter's association), `## Repro evidence` (environment,
steps, artifact, control runs, Expected, Actual), `## Candidate plan`,
`## Candidate plan comment`. Live mode: the issue page and thread, the
student's posted repro comment, the repo's `CONTRIBUTING.md` /
`AI_POLICY.md` / issue template, and the drafts `plan.md` and
`comment.md`.

## Diagnosis and grounding

**Where it lives.** Eval: the sentence or paragraph of the Candidate
plan that states the cause (often opening "Diagnosis:" or "Cause:"),
read against the Repro evidence block: the fenced artifact, each line
beginning "Control" and what it changed, and the Expected/Actual
lines. Any cause proposed in the Issue body or a Thread highlight is
context, not evidence. Live: the diagnosis in `plan.md` against the
artifact and controls in the student's posted repro comment.

**What good looks like.** The stated cause accounts for the artifact
and survives every control: with the blamed component removed or
bypassed the symptom goes away, and with it unchanged the symptom
stays. A cause the controls contradict is wrong however many people in
the thread proposed it (the key-binding diagnosis in a thread versus a
timing matrix that pins highlighting). A cause that cites what the
repro shows ("both controls fit: no background, no subtraction; width
2, no overshoot") is grounded; a cause that only restates the issue's
guess is not yet.

## Scope

**Where it lives.** Eval: the Candidate plan's in-scope statement,
its not-in-scope or deferral lines, the files or areas it names, and
each numbered change or approach step, read against the Issue body
(what was asked) and the Repro evidence's Expected line (what fixed
looks like). Live: the same in `plan.md` against the issue body and
the posted repro.

**What good looks like.** One change, or one change plus explicitly
deferred siblings with a reason each ("Not in scope: option 1, a
larger rework the maintainers may prefer long term"). Every listed
item either fixes the isolated behavior or is named as deferred. A
drive-by rewrite shows up as items the issue never asked for: a
dependency migration, a new setting, a UI rework, a retry framework, a
CI matrix, or anything introduced with "while touching" or "while in
the area". A plan that keeps the right one-line fix and adds four
projects around it is still unbounded. A plan that scopes down to a
subset and says why is bounded.

## Executability

**Where it lives.** Eval: the Candidate plan's file or module names
(a "Files:" line, or paths inline), its chosen approach, and the
order of its steps. Live: the same in `plan.md`.

**What good looks like.** A stranger with a checkout could open a
named file and begin the first step without asking the author
anything. That needs at least one named file or module and one chosen
approach. It fails when the first step is to find out where the
change goes, when the approach is a list of candidates with no pick
("gocui? tcell? not sure"), or when a decision needed to begin is
pushed to build time ("recover() somewhere", "upstream or vendored,
whichever is easier", "poke around the editor code"). An honest
unknown about the exact line inside a named function is fine; an
unknown about which file is not.

## Test plan

**Where it lives.** Eval: the Candidate plan's test plan, read
against the Repro evidence's numbered steps and artifact. Live: the
test plan in `plan.md` against the steps and artifact in the posted
repro comment.

**What good looks like.** The test re-runs the repro (or a test built
from it) and names what will be different afterward: an exit code
("expect exit 0" where the artifact shows 101), an output value, a
test that currently fails or is marked expected-to-fail now passing,
a visible state at a named step ("at step 3 the color must flip"). A
decisive plan can also re-run the controls and expect them unchanged,
but that is confirmation, not proof. "Run the full test suite",
"should feel fast", "nothing else should feel broken", or a test that
passes before the fix as well, proves nothing observable about the
fix.

## Honesty

**Where it lives.** Eval: the Candidate plan's risks, unknowns, or
open-question lines, read against the choices the plan makes that the
Repro evidence does not settle. Live: the same in `plan.md`, plus its
`## Deviations` section once the build has started.

**What good looks like.** The plan names what it has not verified and
what it will do about it: "I have not yet measured the per-print cost;
if it shows up in the benchmark I will move the check", "the exact
clamp site may be one layer up or down; I will confirm while
implementing". False confidence states every choice as settled when
the evidence leaves it open, or asserts a cause the controls do not
support. A terse plan with nothing genuinely unverified is not
dishonest for having no risks section. A mid-build change belongs
under `## Deviations`, in the plan, not only in the diff.

## Comms

**Where it lives.** Eval: the Candidate plan comment (and the plan)
read against the Thread highlights, specifically every comment from an
OWNER, MEMBER, or COLLABORATOR, or from the issue's author, that gives
direction; and both read against the "contribution policy" line under
Repo facts. Live: the draft comment against the live thread, and
against the repo's `CONTRIBUTING.md`, `AI_POLICY.md` or equivalent.

**What good looks like.** Thread-aware means the comment knows what
the maintainers already said: it names the isolated culprit, the
chosen or rejected approach, the posted test build, or the open PR on
the same route, and follows it or explains the departure ("I have read
PR #2089, which takes the same route; if that lands first I will
rebase my tests onto it"). Proceeding as if an owner's "this seems to
be the culprit" and "please test this binary" were not there fails,
however tidy the plan. Where the thread has no maintainer direction,
there is nothing to engage and the comment passes on this point. For
conventions, the test is whether the policy REQUIRES AI disclosure:
when it does, a comment that says nothing about AI fails; when the
policy is silent, conditional on understanding, scoped to pull
requests, or asks only for the contributor's own voice, the comment
passes on this point whatever it says about AI. Course packages are
treated as AI-assisted work.
