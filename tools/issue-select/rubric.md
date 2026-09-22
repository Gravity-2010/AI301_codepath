# Rubric: is this a good first issue?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Maintainer alive | The "last 5 default-branch commits" under Repo facts (each commit's author and date), and each comment's author association in the Comments section (rendered as an uppercase token after the username, e.g. `### name (COLLABORATOR) on 2026-05-11`) | At least one of the last 5 commits is dated within 90 days of the capture date (eval) / today (live) AND is not bot-only activity — a commit whose author ends in `[bot]` counts only when it merges a pull request authored by a human, never when it merges another bot's PR — OR at least one comment from an author with association OWNER, MEMBER, or COLLABORATOR appears within that same window | required |
| Repo in use | The "latest release" and "last push to any branch" under Repo facts, plus "archived:" on the repo line | The repo is not archived, AND (the latest release is within 180 days OR the last push to any branch is within 90 days) | required |
| Scope fits a newcomer | The issue body, its labels, and the Comments section | The issue is not an umbrella/tracking issue listing multiple sub-items, AND no maintainer comment states the fix requires changing core internals, AND the thread shows no still-open, maintainer-unsettled design debate. A `good first issue` label supports a pass but is not required; a bare `help wanted` label alone does not satisfy this on its own | required |
| Nobody already on it | "this issue: assignees:" and "linked PRs:" (with per-PR state) under Repo facts; PR references and claim language ("I'll take this", "working on this", "/assign") in the Comments section | No assignee listed, AND no PR attached to this issue is in an open state — counting both the formally linked PRs under Repo facts and any PR referenced in the comment thread, and a referenced PR whose state is not stated counts as open — AND no claim comment less than 14 days old that went unanswered or was acknowledged by a maintainer | required |
| AI contribution policy | The "contribution policy" line under Repo facts (eval mode), or `CONTRIBUTING.md` / `AI_POLICY.md` / `AI_USAGE_POLICY.md` / PR templates in the repo (live mode) | The policy does not contain an outright ban on AI-generated or AI-assisted contributions. Disclosure requirements, human-review requirements, or a "must personally understand and test" condition all pass. Silence (no policy found) passes | required |
| Clear reproduction or acceptance criteria | The issue body | The body includes concrete steps to reproduce (for a bug) or a stated definition of done (for a feature/cleanup) | preferred |

## Verdict rule

Accept only if every `required` check passes. A single `required` check
graded `fail` or `unclear` rejects the issue — unclear evidence is
treated as a fail. The `preferred` check never changes the verdict;
use it only to rank issues that already passed all required checks.

In eval mode, measure every date-based condition against the bundle's
stated capture date, not the current date. In live mode, measure
against today.
