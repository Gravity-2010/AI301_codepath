# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56

**Verdict output**

Live-mode run over three candidates (#61, #56, #54) from
`codepath/pathreview-ai301-fa26-s3`. All three were accepted; #56 ranked first on the
fit profile and is the issue I am carrying into Unit 2. Its verdict is `accept`, and it
is the first object in the JSON array below.

````
All evidence gathered. Every required check passes for all three, so all are accepted; I rank by the fit profile (Python backend/data, wants real PR-review iteration, avoid frontend).

**Shared facts** (apply to all three):
- **Maintainer alive** — pass: last 5 default-branch commits all by `Aburke225` (human, not `[bot]`), most recent `2026-09-16`, well within 90 days of today.
- **Repo in use** — pass: `archived: false`, last push `2026-09-16` (within 90 days). No release, but push satisfies the OR.
- **AI policy** — pass: no `CONTRIBUTING.md`/`AI_POLICY.md`, PR template silent on AI → silence passes.
- **Nobody already on it** — pass for each: no assignees, repo has zero PRs (nothing linked/open), no comment threads → no claims.

**Ranked read-out (all accepted):**

1. **#56 — Structural chunker silently drops heading-less documents.** Top fit: ingestion/RAG data work, and the fix carries a genuine design choice (chunk as a single block vs. fall back to another strategy) plus a failing test to work against — exactly the room for maintainer feedback and iteration the profile wants. Repro (preferred) passes.
2. **#54 — Resume section detection fails on leading whitespace.** Backend Python parsing (`_detect_sections` regex anchoring), concrete code example + related test failures. Solid fit, slightly less design ambiguity than #56. Repro passes.
3. **#61 — Health-check DB probe passes raw SQL under SQLAlchemy 2.x.** Backend Python and cleanly reproduced (exact error quoted), but it's a near one-liner (`wrap in text()`) — least surface for the review-iteration practice the profile prioritizes. Repro passes.

None are frontend/CSS, so all clear the avoidance; ranking is driven by how much iteration room each offers.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "Last commit by human Aburke225 on 2026-09-16, within 90 days of today"},
      {"name": "Repo in use", "grade": "pass", "evidence": "archived: false; last push 2026-09-16 within 90 days"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "Single bounded bug in StructuralChunker.chunk(); labeled 'good first issue'; no design debate or core-internals warning"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "No assignees; repo has zero PRs; no comments/claims"},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "No CONTRIBUTING/AI policy; PR template silent on AI → silence passes"},
      {"name": "Clear reproduction or acceptance criteria", "grade": "pass", "evidence": "~1000-char headingless doc produces zero chunks; a related failing test exists"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "Last commit by human Aburke225 on 2026-09-16, within 90 days of today"},
      {"name": "Repo in use", "grade": "pass", "evidence": "archived: false; last push 2026-09-16 within 90 days"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "Single bounded bug in _detect_sections() in resume_parser.py; labeled 'good first issue'; no design debate"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "No assignees; repo has zero PRs; no comments/claims"},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "No CONTRIBUTING/AI policy; PR template silent on AI → silence passes"},
      {"name": "Clear reproduction or acceptance criteria", "grade": "pass", "evidence": "Code example: indented resume text returns empty detected_sections; related test failures noted"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "Last commit by human Aburke225 on 2026-09-16, within 90 days of today"},
      {"name": "Repo in use", "grade": "pass", "evidence": "archived: false; last push 2026-09-16 within 90 days"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "Single bounded bug in api/routes/health.py; labeled 'good first issue'; no design debate or core-internals warning"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "No assignees; repo has zero PRs; no comments/claims"},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "No CONTRIBUTING/AI policy; PR template silent on AI → silence passes"},
      {"name": "Clear reproduction or acceptance criteria", "grade": "pass", "evidence": "Body quotes exact SQLAlchemy 2.x error and root cause (raw 'SELECT 1' needs text())"}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

1. Smoke run, `--limit 3` (issue-01, issue-02, issue-03): `agreement: 3/3 scored items`.
   Partial run, so the harness refused to write `eval-run.txt`.
2. Full 20-issue run, written with `--save-run`:
   `agreement: 16/20 scored items  (bar: 18/20: below the bar)`

The second score is the agreement line in the committed `eval-run.txt`.

**Issue analysis**

`issue-01` (conda/conda#16475).

- My rubric decided: **reject**, on the required check `Scope fits a newcomer`.
- Gold label: **accept** (category `clear-accept`; "docs task with a stated home and
  scope; active repo, unclaimed").

Reasoning. The first clause of my scope check reads "The issue is not an
umbrella/tracking issue listing multiple sub-items." The body of issue-01 is organised
under five headed proposals — "Add a new task page", "Update `manage-pkgs.rst`",
"Update `pip-interoperability.rst`", "Update `new-features.md`", and "Consider a global
`troubleshooting.rst` entry" — so the clause fires on the literal shape of the text. The
gold label reads that same structure as one bounded documentation task whose parts all
travel in a single pull request.

`evidence-guide.md` draws the line I dropped: scope fails when the issue is "explicitly
an umbrella or tracking issue (a list of sub-items **meant to be split into separate
work**)". My wording kept "multiple sub-items" and lost "meant to be split into separate
work", so any well-specified multi-file change now reads to my rubric as an umbrella.
That is a test of how the issue is formatted rather than of how much work it is, which is
the opposite of what the guide asks for: "Grade the size of the work being asked for, not
the polish of the writeup."

All four of my disagreements came from this one check. It rejected two issues gold
accepts (`issue-01`, `issue-19`) and accepted two the gold rejects on scope grounds
(`issue-15`, `issue-20`), so it is mis-calibrated in both directions at once — the reason
my `scope` category tally was 2/4 while every other category matched fully.

**Check rationale**

From the `Nobody already on it` row of the `rubric.md` uploaded to
`tools/issue-select/`, the pass condition as it is currently written:

> No assignee listed, AND no PR attached to this issue is in an open state — counting
> both the formally linked PRs under Repo facts and any PR referenced in the comment
> thread, and a referenced PR whose state is not stated counts as open — AND no claim
> comment less than 14 days old that went unanswered or was acknowledged by a maintainer

My first version named PR mentions in the comment thread as evidence but had no clause
that consumed them: the three clauses covered assignees, formally linked PRs, and claim
comments under 14 days old. `calib-04` (sharkdp/bat#1341) falls straight through that
gap. Its repo facts read "linked PRs: none formally linked (PR #3617 is referenced in the
comment thread)", and the claim comment is 151 days old, so no clause fires and the check
passes even though a pull request implementing the feature had been open for five months.

`evidence-guide.md` settles which reading was intended: "Not every PR gets formally
linked; people often just mention their PR in the comments, so read the thread too, and
when the sidebar and the thread disagree, believe the thread." The current wording makes
the PR clause consume both sources. The trailing "a referenced PR whose state is not
stated counts as open" exists so a grader never has to route an unstated state through
`unclear`, which my verdict rule treats as a fail anyway — better to say so explicitly
than to reach the same answer by accident.

**Trade-offs**

What it gives up: the check now blocks an issue whose only attachment is a stale,
abandoned PR. Because an unstated state counts as open, a referenced PR that was in fact
closed unmerged reads as an active claim and the issue is rejected even though it is
genuinely free. I accept that trade deliberately — a false reject costs me one candidate,
while a false accept costs a duplicated PR and a maintainer's time.

Nothing changed elsewhere, and here is how I know. Before making the edit I checked every
bundle the gold labels mark `accept` — `issue-01`, `issue-04`, `issue-06`, `issue-09`,
`issue-11`, `issue-14`, `issue-16`, `issue-19` and `calib-01` — for PR references in the
comment thread. None of the nine has one. The only accept-labelled bundle carrying a
linked PR at all is `issue-09` (conda/conda#11627), and that PR is closed, so "in an open
state" leaves it passing. The new clause can therefore only change results on bundles
that already carried a claim signal, and the full run bears that out: `claimed` matched
4/4, and none of my four disagreements came from this check.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. The issue's fit to your interests and to the time available.
This is Python ingestion and data work, which is where I'm actually comfortable debugging rather than just following a tutorial, and it's not frontend. The scope is one function plus a test that already exists, so it fits the time I have for Unit 2 alongside reproducing the bug.

2. What the verdict identified correctly, and what you weighed that the rubric could not.
The rubric correctly confirmed the repo is alive (human commits through 2026-09-16), that nothing is claimed (no assignees, zero PRs), that the scope is one bounded function, and that there's a reproduction in the body. What it couldn't weigh is that the fix has an unspecified design choice, so the "right" answer depends on a maintainer's opinion, I chose it partly because of that, since I want practice iterating on review feedback. It also passed the AI-policy check on "no CONTRIBUTING.md, so silence passes," but this is a classroom repo, the policy that actually governs me is the course's, not the repo's.

3. The anticipated difficulty in claiming it.
The repo has no PRs at all yet, so nobody has started on anything and there's nothing to race. My scope.md house rule says classmates' claims don't block me anyway. The main risk is that a tier-1 "good first issue" is exactly what everyone else is filtering for too, so being early matters more than being first to comment.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
