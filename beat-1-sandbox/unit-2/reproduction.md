# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

Gravity-2010

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56#issuecomment-5958301360

Hi, this is my first contribution. I'd like to take a look at this issue.

The issue as I read it: `StructuralChunker.chunk()` returns an empty list for a document that has no markdown headings, so a ~1000-character plain-text document produces zero chunks and drops out of the RAG index with no error or warning (the snippet in the issue shows `len(...)` coming back as `0`).

What I'll do next: reproduce this on my machine against `ingestion/chunking/structural_chunker.py` using the issue's own snippet, run `tests/unit/test_structural_chunker.py::test_document_with_no_headings`, and post what I see — the output either way, including if I can't trigger it. I'm not committing to a fix yet; the issue leaves the fallback (single chunk vs. an alternative chunking strategy) open, and I'd rather read the code and hear which direction is preferred before proposing anything.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56#issuecomment-5958347849

Reproduced on the current `main` (`2f4e82f`).

**Environment**

- Ubuntu 22.04.5 LTS, x86_64, kernel 6.8.0-138
- Python 3.13.5 in a fresh venv; dependencies installed with the Makefile's own setup steps (`python3 -m venv .venv`, then `pip install -e ".[dev]"`); tiktoken 0.14.0, pytest 9.1.1
- Repo at commit `2f4e82f` (main, 2026-09-16), unmodified
- I did not start Docker or set `OPENROUTER_API_KEY`. This is a unit-level call on `StructuralChunker` and needs neither.

**Steps** (from an empty directory)

```
git clone https://github.com/codepath/pathreview-ai301-fa26-s3.git pathreview
cd pathreview
python3 -m venv .venv
.venv/bin/pip install -e ".[dev]"
.venv/bin/python -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(len(c.chunk('This is a plain document with no headings at all. ' * 20, {})))"
```

**Observed**

```
0
```

A ~1000-character document with no markdown headings produces zero chunks, matching the issue.

**Control** — the same text with a single heading prepended:

```
.venv/bin/python -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(len(c.chunk('# Title\n' + 'This is a plain document with no headings at all. ' * 20, {})))"
```

```
1
```

So the missing heading is the variable: identical content chunks once a heading exists.

**The repo's own test for this issue**

```
.venv/bin/pytest tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings -v -rx
```

```
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings XFAIL [100%]
XFAIL tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings - issue #56: structural chunker drops documents with no headings
1 xfailed in 0.51s
```

The test is marked `xfail(strict=True)` against this issue, so it currently passes by failing; a fix will need that marker removed.

**Expected:** at least one chunk for non-empty text, so heading-less documents stay in the index.

**Actual:** an empty list, and the document is dropped with no error or warning.

I've looked at `_extract_sections` but haven't traced the exact path yet; I'll confirm once I have a fix candidate. Before proposing one I'd like to know which shape is preferred: a single fallback chunk for the whole document, or handing heading-less text to another chunker.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke run, `--limit 3` (pkg-01, pkg-02, pkg-03): `agreement: 3/3 scored items`.
   Partial; the harness does not write `eval-run.txt` for it.
2. Full 20-package run: `agreement: 18/19 scored items`. One package, `pkg-20`, errored
   inside the harness (`claude exited 1`, empty stderr) on both of its attempts, so the
   harness treated the run as partial and refused to write `eval-run.txt`. The one
   disagreement among the 19 graded was `pkg-12`.
3. `--only pkg-12,pkg-20`: `agreement: 2/2 scored items`. Confirmed the `pkg-20` error was
   transient (it graded reject on `Repo conventions and AI disclosure`) and that `pkg-12`
   graded accept this time with the rubric unchanged. Partial.
4. After revising `Steps re-runnable`, the canary run `--only pkg-12,pkg-18,pkg-20`:
   `agreement: 3/3 scored items`. Partial.
5. Confirming full 20-package run, written with `--save-run`:
   `agreement: 20/20 scored items  (bar: 18/20: PASS)`

The fifth score is the agreement line in the committed `eval-run.txt`.

**Package analysis**

`pkg-12` (prettier/prettier#19795).

- My rubric decided: **reject** on the first full run, failing the required check
  `Steps re-runnable`; then **accept** on a `--only` re-grade of the identical package with
  the identical rubric; then **accept** again after I revised the check.
- Gold label: **accept** (category `clear-accept`; "both shapes reproduced on the current
  release with outputs shown; version delta stated; next step concrete").

Reasoning. The report's steps say it "ran the issue's script verbatim in an empty
directory: `node repro.mjs`" and describe `repro.mjs` as "the issue's two `prettier.format`
calls (shape A: rangeStart 19, rangeEnd 32; shape B: rangeStart 18, rangeEnd 31; both
`parser: "babel"`)". It never shows the file. My original pass condition read "A stranger
holding only the recorded environment and this report could execute every step." Read
literally, that stranger does not have the issue, so a file described only by reference to
the issue is something they cannot obtain, and the check fails. Read the way the gold label
reads it, a stranger on the thread does have the issue, which states both inputs and both
ranges, so the reference leaves nothing to guess, and the check passes.

Sonnet took each reading once. The first full run failed the check (`failed: Steps
re-runnable`). A `--only pkg-12,pkg-20` re-grade of the same package passed it, with the
evidence "all parameters (rangeStart/End, parser) named and matching the issue's own exact
inputs." That is the failure the rubric template warns about: a pass condition two careful
graders can apply and get different answers. The fault was not a threshold but the word
"only", which silently removed the issue from what the reader holds. I rewrote the clause so
the stranger holds "the issue text, the recorded environment, and this report" and an input
may be "identified precisely enough in the issue that the report's reference to it leaves
nothing to guess." The same package then graded accept with the evidence "sufficient for a
stranger holding the issue text to reconstruct."

**Check rationale**

From the `Steps re-runnable` row of the `rubric.md` uploaded to `tools/repro-check/`, the
pass condition as it is currently written:

> A stranger on the issue thread, holding the issue text, the recorded environment, and
> this report, could execute every step from a stated starting state. Each command, input,
> config content, or file is either given in the report or identified precisely enough in
> the issue that the report's reference to it ("the issue's input file", "the issue's
> command with the values it states") leaves nothing to guess. A step fails when it depends
> on something neither the report nor the issue supplies (a private repository, an unshared
> config, "set up the project", "my usual setup"). The number and formatting of the steps
> never matter; only whether each one can be run

Why it reads that way. The first version said the stranger held "only the recorded
environment and this report." I wrote "only" to keep the check honest about private
resources: a step that needs the author's monorepo or an unshared config must fail however
well it is described (`pkg-18`). But "only" also excluded the issue, and a stranger on a
thread always has the issue. That one word made `pkg-12` flip between reject and accept
across two runs of the same rubric. The current wording keeps the private-resource failure
("something neither the report nor the issue supplies") and names the issue as something
the reader holds.

I rejected the alternative of requiring every input to be shown inline. That is a
structure-shaped rule: it would fail a report that correctly points at the issue's own
inputs, and the template says to judge whether a step can be run, not how the write-up is
formatted. The last sentence of the check is there for the same reason.

**Trade-offs**

What it gives up. The check now trusts a report's pointer into the issue. A report could
say "ran the issue's command" when the issue's command was itself incomplete or ambiguous,
and this check would pass it; that mismatch would have to be caught by `Behavior shown
matches the issue`, which reads the artifact against the issue's behavior rather than the
steps against the issue's text. I accept that trade: the alternative was a check that
failed correct reports for not re-typing what the issue already says.

This was a loosening, so before the confirming full run I ran the canary the eval README
asks for: `--only pkg-12,pkg-18,pkg-20`. `pkg-18` is the package that fails this check for
the right reason (steps that live in a private monorepo with an unshared config) and had
to stay reject. `pkg-20` is the single-package `disclosure` category the README names as
the live canary. Result: 3/3. `pkg-12` accept; `pkg-18` reject on `Steps re-runnable`
("Step 1 is a private monorepo the author 'cannot share'; step 2 is an internal config
that is 'also not shareable'... no stranger can execute any step"); `pkg-20` reject on
`Repo conventions and AI disclosure`.

Nothing else changed, and here is how I know: this loosening can only rescue a package
whose steps become runnable once the reader also holds the issue. The other packages that
fail `Steps re-runnable` either have no steps at all (`pkg-04`, `pkg-13`, `calib-02`) or
steps the issue cannot supply (`pkg-18`), so none of them gains anything from it. The one
package it plausibly touches is `pkg-06`, whose steps omit the driver the issue names; even
if a grader now reads the driver in from the issue, `pkg-06` still fails `Environment
recorded` (no OS, driver, or minikube version in the report), so its verdict cannot move.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
