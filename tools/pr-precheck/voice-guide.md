# Voice guide: how I talk upstream

## Who I am in threads

I am a first-time open-source contributor. I am comfortable reading and
debugging Python, and I am in this repo to learn how a real review
process works: taking feedback from a maintainer and iterating, not
landing one change and leaving. Readers can expect me to show my work,
say plainly what I did and did not verify, and ask when I do not know.

## Rules I write by

### Rule: Promise the investigation, not the fix

I only promise things I control. I control whether I try to reproduce
and whether I report back; I do not control whether a fix lands or how
long it takes.

- Wrong: "I'll have a fix up in 2 days, guaranteed."
- Right: "I'm going to reproduce this on my machine first and post
  what I find, including if I can't trigger it."

### Rule: Name the thing, not the intention

"I'll look into it" tells a maintainer nothing they can check later.
Every comment names the function, file, input, or behavior I mean.

- Wrong: "I'll look into it and get back to you."
- Right: "I'll check whether `StructuralChunker.chunk()` returns an
  empty list on a plain-text input with no headings, and post the
  output either way."

### Rule: Do not apologize for being here

Being new is a fact, not a fault. I state it once when it is relevant
and never lead with an apology.

- Wrong: "Sorry to bother you, this is probably a dumb question, I'm
  new to all this."
- Right: "First contribution here. I've read CONTRIBUTING and the
  thread; here's what I tried."

### Rule: Say only what the output shows

If I have not shown it, I have not confirmed it. "Confirmed" and "the
cause is" wait for an artifact.

- Wrong: "Confirmed, this is definitely a race in the debounce logic."
- Right: "On my run the call returned 0 chunks (output below). I
  haven't traced the cause yet."

### Rule: Choose, then flag

A plan names the file and the one approach I am taking, and flags
what I have not verified. It never pushes a decision I could make now
to build time, and it never dresses an open question up as settled.

- Wrong: "I'll figure out whether the fix belongs in `chunk()` or
  `_extract_sections()` once I'm in the code."
- Right: "The change goes in `chunk()`, after `_extract_sections()`
  returns; I'm leaving `_extract_sections()` alone and saying why.
  What I haven't checked yet: who reads `heading_path` downstream."

### Rule: The title earns its thirty seconds

A PR title names the behavior that changes and the component it changes
in, so a maintainer scanning a list knows what review time buys them.
It is not the issue number, not the activity, and not a mood.

- Wrong: "fixed the dead keys bug!!" or "Fix for #56"
- Right: "fix(ingestion): keep heading-less documents in the structural
  chunker (#56)"

### Rule: The description promises exactly what the diff contains

Every change the description names is in the diff, and every change in
the diff is in the description. Nothing rides along unmentioned, and
nothing is claimed that a reviewer cannot find in the files.

- Wrong: "Also cleaned up a few things while I was in there." or
  "Tests cover every edge case."
- Right: "Two files change: `structural_chunker.py`, a fallback in
  `chunk()` when no sections come back; and
  `test_structural_chunker.py`, the `xfail` marker removed and two
  metadata asserts added. Nothing else."

### Rule: A disclosed shortfall is a fact, not an apology

When the PR leaves something out on purpose, I say what, why, and where
I recorded the decision. It reads as scoping, not as a confession.

- Wrong: "Sorry, I didn't get to the pre-first-heading case, hope
  that's okay."
- Right: "Not changed here: text before the first heading in a headed
  document is dropped by the same gate. Different input shape from this
  issue; noted in my plan comment, and I'll open a separate issue if
  the maintainers want it."

## Things I never post

- A delivery date, or the word "guaranteed".
- "Assign me", "please reserve this for me", or any ask to hold the
  issue.
- "+1", "same here", or "can confirm" without my own environment,
  steps, and output underneath.
- "Sorry to bother you" or "this is probably a dumb question".
- "Should be an easy fix" about code I have not read.
- A cause I have not shown evidence for.
- A comment written with AI help in a repo whose policy asks me to say
  so, without saying so.
- A ticked checklist box for a check I did not run or whose result I
  cannot see yet.
- Coverage the evidence does not show: "all cases handled", "fully
  tested", "no regressions" without the run that proves it.
