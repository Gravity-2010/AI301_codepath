# Plan: issue #56 — structural chunker drops documents with no headings

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56
Repro: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56#issuecomment-5958347849
Branch: `fix/56-structural-chunker-headingless-fallback`

## Diagnosis

`StructuralChunker.chunk()` returns `[]` for any non-empty document that
contains no markdown heading. The early return at the top of `chunk()`
handles only empty or whitespace-only text; everything else goes to
`_extract_sections()`, and `_extract_sections()` only ever collects a
content line when a heading has already been seen:

```python
# ingestion/chunking/structural_chunker.py, _extract_sections
else:
    # Regular content line
    if heading_stack or current_section_lines:  # Only collect if we have a heading
        current_section_lines.append(line)
```

With no heading, `heading_stack` stays empty and `current_section_lines`
never gets its first line, so `sections` comes back empty and the loop
in `chunk()` has nothing to iterate. The document is dropped with no
error or warning.

This follows from my reproduction (linked above), on commit `2f4e82f`,
Python 3.13.5, tiktoken 0.14.0:

- The issue's own snippet, a ~1000-character plain document with no
  headings: `len(c.chunk(..., {}))` printed `0`.
- Control, the same text with `# Title\n` prepended: printed `1`. The
  missing heading is the only variable, so the content itself is not
  the problem; the heading gate is.
- The repo's own test for this issue:
  `tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings`
  ran as `XFAIL ... issue #56: structural chunker drops documents with no headings`.
  It is marked `@pytest.mark.xfail(strict=True, reason="issue #56: ...")`,
  so today it passes by failing.

## Scope

In scope:

- `ingestion/chunking/structural_chunker.py`: make `chunk()` produce at
  least one chunk for any non-empty document, including one with no
  headings.
- `tests/unit/test_structural_chunker.py`: remove the `xfail` marker
  from `test_document_with_no_headings` (with `strict=True`, a passing
  test under that marker is a failure), and tighten its assertions to
  the metadata the fallback produces.

Not in scope, deliberately:

- `SemanticChunker` and `strategy_selector.py`: unchanged. I use the
  semantic chunker only through the path `chunk()` already takes for
  oversized sections.
- How documents *with* headings are chunked: unchanged. The control
  run (`1` chunk with a heading) must still print `1`.
- The `Chunk` metadata schema: no new keys. Headingless chunks carry
  `heading_path=""` and `heading_level=0`, which are already the
  types those keys hold.
- A sibling behavior I noticed while reading `_extract_sections()`:
  text that appears *before the first heading* in a headed document is
  also dropped by the same gate. That is a different input shape from
  the one this issue reports and the one my repro shows, so I am not
  changing it here. I will note it on the issue as a possible
  follow-up rather than fold it in.

## Files

- `ingestion/chunking/structural_chunker.py` — `chunk()` only.
- `tests/unit/test_structural_chunker.py` — `test_document_with_no_headings`.

## Approach

The issue leaves the fallback shape open ("chunked as a single block
or using an alternative strategy"), and my repro comment asked which
is preferred. The thread has no maintainer direction on it yet (all
comments so far are from students), so I am choosing and stating the
choice:

1. In `chunk()`, after `sections = self._extract_sections(text)`, if
   `sections` is empty (and the text is non-empty, which the early
   return already guarantees), synthesize one section covering the
   whole document: `{"content": text.strip(), "path": [], "level": 0}`.
2. Let that synthetic section flow through the existing per-section
   loop unchanged. That loop already decides, by `SECTION_TOKEN_LIMIT`
   (800 tokens), whether a section becomes one `Chunk` or is handed to
   `SemanticChunker` for sub-chunking. So a short headingless document
   becomes a single chunk, and a long one is sub-chunked semantically,
   using the code path that already exists for long headed sections.
   This answers the issue's either/or without adding a new strategy:
   it is "single block" when a single block fits, and the existing
   alternative when it does not.
3. `heading_path` for these chunks is `" > ".join([])`, i.e. `""`,
   and `heading_level` is `0`, both produced by the existing loop with
   no special-casing.
4. In the test, remove the `xfail` decorator and assert, in addition
   to what is there now: `result[0].metadata["heading_path"] == ""`
   and `result[0].metadata["heading_level"] == 0`.

I am not changing `_extract_sections()` itself. Fixing the gate there
would also change the pre-first-heading behavior named above, which I
have scoped out.

## Test plan

Re-run my Unit 2 repro steps against the branch, from the fork clone
with the same venv:

1. The issue's snippet:
   `.venv/bin/python -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(len(c.chunk('This is a plain document with no headings at all. ' * 20, {})))"`
   Before: `0`. **Expected after: `1`.**
2. The control, same text with `# Title\n` prepended. Before: `1`.
   **Expected after: `1`** (unchanged; headed documents are not
   touched).
3. The repo's test:
   `.venv/bin/pytest tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings -v`
   Before: `XFAIL`. **Expected after: `PASSED`**, with the marker
   removed.
4. The whole chunker test file:
   `.venv/bin/pytest tests/unit/test_structural_chunker.py -v`
   Before: 14 passed, 1 xfailed. **Expected after: 15 passed**, no
   xfail, no xpass.
5. One new observable for the design choice, a headingless document
   longer than `SECTION_TOKEN_LIMIT`:
   `print(len(c.chunk('This is a plain document with no headings at all. ' * 200, {})))`
   Before: `0`. **Expected after: a value greater than `1`**, showing
   the oversized fallback section was handed to the semantic
   sub-chunker rather than emitted as one block.

## Risks and unknowns

- **Fallback shape is my choice, not the maintainers'.** No maintainer
  has answered the either/or. If review prefers a different shape
  (for example, always a single block regardless of length), the
  change is confined to step 1-2 of the approach and the test in
  step 5; I will say so in the PR and flag it for the reviewer.
- **Consumers of `heading_path`.** Downstream RAG code may assume
  `heading_path` is non-empty. Before building I will grep the `rag/`
  and `ingestion/` packages for `heading_path` and `heading_level`
  readers and note what I find in Deviations; if a reader would break
  on `""`, that becomes a scoped decision I record there.
- **Semantic sub-chunking of the fallback.** Step 5's expected value
  depends on `SemanticChunker` splitting a ~10,000-character
  paragraph into more than one chunk. I have not run it on this
  input; if it returns one chunk, I will record that and adjust the
  test's threshold rather than the production code.
- **Pre-first-heading text.** Named above as out of scope. The risk is
  that a reviewer sees it as the same bug; I will point at it on the
  issue so the decision is visible.

## Deviations

Nothing changed; the plan held. The build is the two edits the plan
named and no others: the fallback in `chunk()` right after
`_extract_sections()` returns, and the test's `xfail` marker removed
with the two metadata assertions added. `_extract_sections()` is
untouched, as planned. Every test-plan step produced the value the plan
predicted (`0` → `1`, control `1` → `1`, `XFAIL` → `PASSED`,
`14 passed, 1 xfailed` → `15 passed`, 2201-token input `0` → `5`).

Two things the plan listed as unknowns, checked during the build and
recorded here so the decision is visible:

- **`heading_path` readers.** I grepped `rag/`, `ingestion/`, `agent/`,
  `api/`, `core/`, and `safety/` for `heading_path` and
  `heading_level`. Outside `structural_chunker.py` and its test, the
  only mention is a docstring in `ingestion/chunking/base.py`. Nothing
  reads the value, so `""` on headingless chunks breaks nothing today.
  Risk closed; no change needed.
- **Semantic sub-chunking of a long headingless input.** The
  2201-token input produced 5 chunks, so the oversized fallback section
  did go through `SemanticChunker` as the plan expected. The test-plan
  threshold (`> 1`) held; nothing to adjust.

One piece of context, not a deviation: a classmate's PR #81 on this
issue fixes it inside `_extract_sections()`, so heading-less content
becomes a single section there. My change sits in `chunk()` and leaves
`_extract_sections()` alone, which is why the pre-first-heading
behavior I scoped out stays untouched here. Same observable, different
site; per the house rules I proceeded with my own plan.
