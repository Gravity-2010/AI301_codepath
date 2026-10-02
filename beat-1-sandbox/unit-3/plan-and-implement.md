# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

Gravity-2010

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56#issuecomment-5958919355

Plan for this one, built from my repro above (`0` chunks for the issue's snippet, `1` with a heading prepended, and the repo's `test_document_with_no_headings` running as `XFAIL`).

**Cause.** `_extract_sections()` only collects a content line once a heading has been seen (`if heading_stack or current_section_lines:`), so a document with no heading produces no sections and `chunk()` returns `[]`. The control run isolates the heading gate: identical content chunks once a heading exists.

**Change, one bounded fix in `ingestion/chunking/structural_chunker.py`.** In `chunk()`, when `_extract_sections()` returns nothing for non-empty text, synthesize one section covering the whole document (`path=[]`, `level=0`) and let it go through the existing per-section loop. That loop already sends sections over `SECTION_TOKEN_LIMIT` to `SemanticChunker`, so a short headingless document becomes a single chunk and a long one gets sub-chunked by the path that already exists. Nobody has answered the single-block-vs-alternative question yet, so this is my choice and I'm flagging it: it uses "single block" when one fits and the existing alternative when it doesn't, with no new strategy.

Not touching: `SemanticChunker`, `strategy_selector.py`, how headed documents chunk, or the `Chunk` metadata keys. One sibling I noticed and am leaving alone: text *before the first heading* in a headed document is dropped by the same gate. Different input shape from this issue; worth its own issue if others agree.

**Test.** Remove the `xfail` marker from `test_document_with_no_headings` and re-run my repro: the snippet should print `1` (was `0`), the heading control stays `1`, the test goes `XFAIL` → `PASSED`, and a headingless input over 800 tokens should give more than one chunk.

**Open.** I haven't yet checked what reads `heading_path` downstream; headingless chunks will carry `""`. I'll grep `rag/` and `ingestion/` before building and record what I find.

---

## Your branch

**Branch**

`fix/56-structural-chunker-headingless-fallback`

**Evidence**

Before: on `main` at `2f4e82f`, unmodified. Steps 1–3 are the artifacts from my Unit 2
repro comment (https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56#issuecomment-5958347849);
steps 4–5 were captured on the same clean `main` before the branch was created, because
the plan's test plan added them.

```
$ .venv/bin/python -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(len(c.chunk('This is a plain document with no headings at all. ' * 20, {})))"
0

$ .venv/bin/python -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(len(c.chunk('# Title\n' + 'This is a plain document with no headings at all. ' * 20, {})))"
1

$ .venv/bin/pytest tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings -v -rx
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings XFAIL [100%]
XFAIL tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings - issue #56: structural chunker drops documents with no headings
1 xfailed in 0.51s

$ .venv/bin/pytest tests/unit/test_structural_chunker.py -v 2>&1 | tail -1
======================== 14 passed, 1 xfailed in 0.19s =========================

$ .venv/bin/python -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(len(c.chunk('This is a plain document with no headings at all. ' * 200, {})))"
0
```

After: on branch `fix/56-structural-chunker-headingless-fallback`, the two edits applied
(`ingestion/chunking/structural_chunker.py` fallback in `chunk()`; `xfail` marker removed
from `test_document_with_no_headings`), same venv.

```
$ .venv/bin/python -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(len(c.chunk('This is a plain document with no headings at all. ' * 20, {})))"
1

$ .venv/bin/python -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(len(c.chunk('# Title\n' + 'This is a plain document with no headings at all. ' * 20, {})))"
1

$ .venv/bin/pytest tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings -v
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings PASSED [100%]
============================== 1 passed in 0.19s ===============================

$ .venv/bin/pytest tests/unit/test_structural_chunker.py -v 2>&1 | tail -1
============================== 15 passed in 0.18s ==============================

$ .venv/bin/python -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(len(c.chunk('This is a plain document with no headings at all. ' * 200, {})))"
5

$ .venv/bin/python -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(c.chunk('This is a plain document with no headings at all. ' * 20, {})[0].metadata)"
{'heading_path': '', 'heading_level': 0, 'chunk_index': 0, 'char_start': 0, 'char_end': 999}
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke run, `--limit 3` (pkg-01, pkg-02, pkg-03): `agreement: 3/3 scored items`.
   Partial; the harness does not write `eval-run.txt` for it.
2. Full 20-package run, written with `--save-run`:
   `agreement: 19/20 scored items  (bar: 18/20: PASS)`

The second score is the agreement line in the committed `eval-run.txt`. I did not run the
harness again after it: the one disagreement (`pkg-14`) is one of the four packages the
assignment says could reasonably go either way, and re-grading it would have cost another
confirming full run for a point the bar does not award. It is the package analysed below.

**Package analysis**

`pkg-14` (zellij-org/zellij#5174).

- My rubric decided: **reject**, on the required check `A stranger could start`.
- Gold label: **accept** (category `clear-accept`; "honestly scoped-down: reattach
  handshake fix with a regression-window repro; defers the untestable Windows variant and
  says so; arguable on the deferral, ready as scoped").

Reasoning. The plan's "Files:" line reads: "the client attach/reattach path in
`zellij-server` (session connection handling) and `zellij-client`'s terminal query
issuance; exact functions to be pinned in the PR after tracing the query issuance with
debug logs, which I have working." My check asks for "at least one specific file or module
to change and one chosen approach," says "A plan whose first step is to find out where the
change goes fails," and carves out only "a named unknown about the exact line or
sub-location inside a named file or function." The plan names two crates and a path
through them but no file, and its stated first move is to trace where the queries are
issued. Read against the pass condition, that is a plan whose starting step is locating
the change, which the check fails; the carve-out does not reach it because there is no
named file for the unknown to sit inside.

Gold reads the same lines as ready: the crate and the handshake path are named, the
approach (drain pending OSC responses before pane input is wired) is chosen, the debug
tracing already works, and the unknown is stated rather than hidden. That is a reasonable
reading. The disagreement is over where "named module" ends and "find out where" begins,
and gold itself calls the package arguable. My check draws that line at a file or a
function; this plan sits one step above it.

**Check rationale**

From the `A stranger could start` row of the `rubric.md` uploaded to `tools/plan-check/`,
the pass condition as it is currently written:

> The plan names at least one specific file or module to change and one chosen approach
> to the change, so that a stranger with the repo could open the right file and begin. No
> decision needed to begin is deferred to build time: "whichever is easier", "somewhere",
> "poke around", "investigate and then decide", "gocui? tcell? not sure" each fail. A plan
> whose first step is to find out where the change goes fails. A named unknown about the
> exact line or sub-location inside a named file or function does not fail this check

Why it reads that way. The unbuildable packages fail in two different ways and the check
had to catch both: no location at all (`pkg-10`'s "dig into where the time goes",
`calib-02`'s "poke around the editor code") and a location with every real decision pushed
to build time (`pkg-18`'s "recover() somewhere... upstream or vendored, whichever is
easier", `pkg-17`'s "gocui? tcell? not sure"). "Names a file or module and a chosen
approach" catches the first; "no decision needed to begin is deferred" catches the second,
and the quoted phrases are there so an executor recognises the pattern instead of judging
tone.

The last sentence is the part I added after reading `pkg-13`, a clear accept whose plan
says "the exact fix site within the branch may move one level during implementation."
That is an honest unknown inside a named function (`AdaptDispatch::EraseInDisplay`), not a
deferred decision, and without the carve-out the check would have failed it. I rejected
two alternatives: requiring exact functions (fails `pkg-13` and most honest plans), and
accepting a named area with no file (lets a plan start with "trace where this happens",
which is `pkg-10`'s shape with better vocabulary). The line sits at "file or function
named"; `pkg-14` shows that line is strict enough to cost one arguable package.

**Trade-offs**

What it gives up: `pkg-14`. The plan names two crates and the path through them, has a
chosen approach, and has debug tracing already working, but its "Files:" line defers the
exact functions to the PR "after tracing the query issuance." My check reads that as a plan
whose first step is to find out where the change goes, and holds it; gold accepts it as
honestly scoped. I accept that this check will miss plans of that shape, where the author
knows the crate and the mechanism but has not yet opened the file. In a classroom repo
that is a reasonable thing to ask a planner to do before posting; in a large codebase a
maintainer might not care. I am keeping the stricter reading and recording the cost.

I considered the loosening that would pass it: "names a specific file, function, or
module." On the full run's table the `unbuildable` packages are `pkg-10`, `pkg-17`, and
`pkg-18`, and none of them names a module together with a chosen approach, so on paper the
loosening would not flip them. But it would still need those three as canaries on a
`--only` run and then a second confirming full run (about $4) to replace `eval-run.txt`,
for one package the assignment labels arguable and a bar that was already passed. I left
the check as written and spent nothing.

Nothing else changed, and here is how I know: no check was revised between the smoke run
and the confirming run, so the committed `eval-run.txt` is the first and only full run of
these files. Its category line reads `clear-accept 6/7  scope-creep 4/4
thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`: the single miss is `pkg-14`,
and the floor held everywhere, including the two-package `thread-convention` category
the assignment names as the one a rubric cannot buy back on volume.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
