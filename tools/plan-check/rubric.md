# Rubric: is this plan ready to post and build from?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Diagnosis grounded in the evidence | The plan's stated cause, read against the Repro evidence block: its artifact (the exact error, output, or symptom), every control run and what each one rules in or out, and the Expected/Actual lines; plus any cause proposed in the Issue or Thread highlights | The plan states a cause, that cause explains the behavior the repro artifact shows, and no control run or step in the repro evidence rules it out. A cause that a control excludes (the symptom persists with the blamed component removed or bypassed, or vanishes with it unchanged) fails, however confidently it is stated and even when a thread comment proposed it first. A plan with no stated cause fails | required |
| One bounded change | The plan's in-scope and not-in-scope statements, the files or areas it names, and each listed change or approach step, read against what the Issue asks for and what the Repro evidence isolates as the behavior to fix | Every listed change either fixes the behavior the repro isolates or is a stated deferral with a reason. Work the issue did not ask for fails the check even when the core fix among it is right: a dependency migration, a new option or setting, a UI rework, a refactor or redesign, a retry or caching framework, a CI matrix, or anything introduced with "while in the area" or "while touching". A docs-only workaround for the symptom fails when the repro or the thread has isolated a code cause. A plan that narrows to a subset of the full fix and says why passes | required |
| A stranger could start | The files, modules, or functions the plan names; the approach it chooses; the order of work it gives | The plan names at least one specific file or module to change and one chosen approach to the change, so that a stranger with the repo could open the right file and begin. No decision needed to begin is deferred to build time: "whichever is easier", "somewhere", "poke around", "investigate and then decide", "gocui? tcell? not sure" each fail. A plan whose first step is to find out where the change goes fails. A named unknown about the exact line or sub-location inside a named file or function does not fail this check | required |
| Decisive test plan | The plan's test plan, read against the Repro evidence block's steps and artifacts | The test plan names at least one observable outcome specific to this fix that re-running the repro's steps (or a test built from them) would show: a stated exit code, output value, test result, or visible change at a named step, with the expected post-fix value given. "Run the full test suite", "should feel fast", "nothing else should feel broken", or any test that would already pass before the fix, fails. Re-running the repro's own control runs as unchanged is a pass when it accompanies a fix-specific observable, not on its own | required |
| Engages the thread | The Candidate plan comment and Candidate plan, read against the Thread highlights: every comment from an OWNER, MEMBER, or COLLABORATOR, or from the issue's author, that gives direction (an isolated culprit or file, a chosen or rejected approach, a posted test build or pull request, a request to test or report something) | Where the thread contains such direction, the plan or the comment names it and either follows it or says why it departs from it. Where the thread contains no maintainer or author direction, this check passes. A plan or comment that proceeds as if a stated culprit, a posted fix, an open PR on the same route, or an explicit request were not there fails, however bounded the plan is otherwise | required |
| Repo conventions and AI disclosure | The "contribution policy" line under Repo facts, read against the Candidate plan comment and Candidate plan | If the stated policy REQUIRES disclosing AI assistance in comments or contributions, the plan or the comment states that AI assistance was used and to what extent. If the policy is silent on AI, permits AI use without a disclosure requirement, conditions AI use only on the human understanding the work, asks only that comments be in the contributor's own voice, or scopes its disclosure ask to pull requests, this check passes regardless of what the comments say about AI. "Requires" means the policy text makes disclosure mandatory; a policy that merely mentions AI does not | required |
| Risks and unknowns named | The plan's stated risks, unknowns, or open questions, read against the decisions the plan makes that the repro evidence does not settle | The plan names at least one thing it has not verified or one way the change could go wrong, and says what it will do about it (measure, gate, check during build, flag for review). A plan that states every choice as settled when the repro evidence leaves it open does not earn this; a terse plan with nothing unverified in it is not penalised, because this check never changes the verdict | preferred |

## Verdict rule

Accept (ready to post and build from) only if every `required` check
passes. A single `required` check graded `fail` or `unclear` rejects the
package. `unclear` is treated as `fail`: a plan whose cause, scope,
starting point, or test I cannot verify from the package is a plan that
is not ready to build from. The `preferred` check never changes the
verdict; it only marks a stronger plan.

The deciding check is the first `required` check, in table order, that
did not pass; its evidence line is what the summary quotes. When every
required check passes, the summary names the fix-specific observable
from the test plan as the thing a reviewer can hold the build to.

Eval mode grades the complete package with every check. Live mode grades
`plan.md` and the draft comment the same way, and after a recorded
deviation grades the updated `plan.md` again, with the Deviations
section read as part of the plan for the first four checks.
