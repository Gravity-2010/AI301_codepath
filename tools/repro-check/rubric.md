# Rubric: is this reproduction package ready to post?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Environment recorded | The environment line(s) of the candidate repro report; the version and platform the Issue section names as the bug's target; the "bug reports:" line under Repo facts for any environment fact the repo's own template asks for (driver, backend, install method) | The report names the operating system and the project version it actually ran, plus any environment fact the issue or the repo's bug template treats as relevant to this bug (for example the driver on a driver-specific issue). The version matches what the issue targets, OR the report explicitly names the difference ("filed against 1.9.4; still present on 1.11.7"). A report with no environment record, or one run on a different version without saying so, fails | required |
| Steps re-runnable | The steps of the candidate repro report, read against the trigger the Issue section describes (its commands, inputs, config, or user actions) | A stranger on the issue thread, holding the issue text, the recorded environment, and this report, could execute every step from a stated starting state. Each command, input, config content, or file is either given in the report or identified precisely enough in the issue that the report's reference to it ("the issue's input file", "the issue's command with the values it states") leaves nothing to guess. A step fails when it depends on something neither the report nor the issue supplies (a private repository, an unshared config, "set up the project", "my usual setup"). The number and formatting of the steps never matter; only whether each one can be run | required |
| Behavior shown matches the issue | The artifacts in the candidate repro report (output excerpts, logs, screenshots, exit codes), read against the specific behavior the Issue section describes: its error text, exit code, or visible symptom | At least one artifact displays the behavior the issue reports, the same failure and not an adjacent one: the same error message or panic, the same exit code, the same visible symptom. An artifact that only shows the program running, a different error, a graceful validation message where the issue reports a crash, or no artifact at all, fails. EXCEPTION: a report that plainly states it could NOT reproduce passes this check when its artifacts show a genuine attempt at the issue's own trigger (the issue's commands or inputs, or a faithful re-creation of them) | required |
| Outcome stated honestly | Every statement of result in the candidate repro report ("reproduced", "confirmed", "crashes", "the cause is", "could not reproduce"), read against the report's own artifacts | Each claim of what happened is backed by an artifact shown in the report, and the wording does not exceed what that artifact shows: no root cause asserted without shown evidence, no "consistent on every machine" from one run, no crash claimed over an artifact showing the program still alive, no generalizing to platforms or versions that were not run. A cannot-reproduce passes when it says so plainly and names what differed from the issue's conditions. Confidence words ("definitely", "guaranteed", "as you can see") count against the report only when the artifact does not show what they assert | required |
| Claim specific and honest in intent | The candidate claim comment, read against the title and body in the Issue section and the Thread highlights | The claim names at least one fact specific to this issue (its symptom, error, component, version, or a detail from the thread) that could not be pasted onto a different issue, and says what the author has done or will do about it (reproduced it, will investigate a named part). It promises only investigation and a report. A claim that guarantees a fix, names a delivery date, asks to be assigned or to have the issue reserved, or consists of "+1 / same here / I would love to contribute" boilerplate with nothing issue-specific, fails | required |
| Repo conventions and AI disclosure | The "contribution policy" line under Repo facts, read against the candidate claim comment and repro report | If the stated policy REQUIRES disclosing AI assistance in comments or contributions, at least one of the two comments states that AI assistance was used and to what extent. If the policy is silent on AI, permits AI use without a disclosure requirement, conditions AI use only on the human understanding the work, or states that its disclosure ask applies to pull requests and not to issue comments, this check passes regardless of what the comments say about AI. "Requires" means the policy text makes disclosure mandatory; a policy that merely mentions AI does not | required |
| Control run shown | The candidate repro report's artifacts | The report includes a contrasting run (the non-failing input, the other version, the other setting) whose artifact shows the behavior NOT occurring, so a reader can see that the trigger is the variable that matters | preferred |

## Verdict rule

Accept (ready to post) only if every `required` check passes. A single
`required` check graded `fail` or `unclear` rejects the package. `unclear`
is treated as `fail`: proof I cannot verify is proof that is not ready to
post. The `preferred` check never changes the verdict; it only marks a
stronger package.

Live mode, claim-only draft: the four checks whose evidence is the repro
report (Environment recorded, Steps re-runnable, Behavior shown matches
the issue, Outcome stated honestly) are reported as `unclear` with
evidence `not yet applicable: claim-only draft` and are left out of the
verdict rule. The verdict then rests on Claim specific and honest in
intent and Repo conventions and AI disclosure alone, and answers only:
is this claim comment ready to post?

Eval mode always grades the complete package with every check.
