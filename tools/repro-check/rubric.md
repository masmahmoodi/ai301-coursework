# Rubric: is this reproduction package ready to post?

Seven required checks, one per way a package gets posted before it is
ready, plus two preferred checks that never move a verdict.

The deciding question for most of them is the same: does the artifact
the report shows display the failure the issue describes, produced by
the trigger the issue names? Everything else supports that question.

Throughout, "the software under test" means the tool or library the
issue is filed against, and "the issue's target" means the version and
platform the issue states it was seen on.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| env-recorded | The repro report's environment record, read against the issue's own environment line and the repo-facts "bug reports" template asks. | Pass if the report names the version or build of the software under test AND the operating system or platform it ran on. Also required, when the issue's own text makes some other axis the trigger, is that axis: the browser and its language order, the shell, the VM driver, the terminal, or the build profile (debug versus release) when the issue says the failure mode differs between them. Fail when the report carries no environment record at all, or when it omits an axis the issue itself names as the condition for the failure. A record spread across a table, a prose line, or the steps themselves all counts; only the facts matter, not where they sit. | required |
| steps-rerunnable | The repro report's steps and commands, read for whether a reader could reach the same starting state and fire the same trigger, and for whether the inputs those steps need are obtainable. | Pass if the report gives the concrete commands or UI actions to run, AND the inputs they consume are either shown inline, small enough to retype from the report, or publicly obtainable such as a published package, a playground link, or a file the issue itself supplies. Fail when any step depends on material the reader cannot get, such as a private repository, an internal config file, or a proprietary data set described but not shared. Fail when a step exists only as prose with no command or action a reader could perform. Fail when the steps never fire the trigger the issue names, for example starting the tool with defaults on an issue whose trigger is a specific driver or flag. | required |
| artifact-shows-issue-behavior | The output excerpts, logs, screenshots, or transcripts in the repro report, read line by line against the specific failure the issue describes: its error text, its exit code, its panic message, or its wrong output. | Pass in either of two ways. First: the report contains at least one artifact that displays the issue's own symptom, produced by the trigger the issue names. The symptom shown and the symptom reported must be the same failure mode, not merely both failures; a graceful validation error is not a panic, a compile error is not a runtime path error, and garbled output with the process still alive is not a crash. Second, the honest cannot-reproduce path: the report states plainly that the symptom did not appear, and shows the artifact of the real attempt, meaning the trigger was actually run and its output is displayed. A cannot-reproduce backed by a real attempt passes this check, because evidence that the trigger ran and produced no symptom is on-target evidence. Fail when the report contains no artifact at all, when the only artifacts show that the software is installed or that a session started rather than the reported symptom, or when the artifact shows a different failure mode than the issue describes. | required |
| deviation-named | The report's version and platform facts, compared with the version and platform the issue states it was confirmed on, together with any sentence in the report that names a difference. | Pass when the report ran on the issue's target or on a newer or current release, or when the report ran somewhere different and says so in words, naming what differed. One sentence is enough: naming the delta is the whole requirement. Fail when the report ran on an older version, a different operating system, a different install method, or a different build profile than the issue's stated target and never mentions it, because a silent deviation turns the artifact into evidence about a different world than the one the issue is about. | required |
| outcome-stated-honestly | The report's concluding sentences and its "Actual" line, plus any assertion in the claim comment, read against what the artifacts in this same package actually show. | Pass when every statement about the outcome is carried by an artifact in the package: a reproduction claimed is a symptom shown, a cannot-reproduce is said plainly rather than implied to be a success, and any root cause named is either demonstrated by an artifact or marked as a guess with words such as "looks like", "suggests", or "my hypothesis". Fail when the report narrates a confirmation its artifacts do not support, when it asserts a diagnosis as verified with no artifact behind it, or when it reaches for certainty language such as "100 percent reproducible", "guaranteed", "conclusively", or a repeat count as a substitute for showing the symptom. Repetition counts and confident tone are not evidence. | required |
| claim-comment-specific | The candidate claim comment, read against this issue and the repo-facts template asks. | Pass when the claim comment is about this issue in particular, naming at least one of: the symptom it will investigate, the version it saw, the file or area it will read, or the concrete next step it will take. It must also promise only what the writer controls. Fail when the comment could be pasted unchanged onto any issue in any repository, such as a bare plus-one, a praise-and-assign-me request, or a me-too with no stated intent. Fail when it promises a fix, a deadline, or a guaranteed outcome, since none of those are the writer's to promise. Fail when it asserts a reproduction that the package's own artifacts do not support. | required |
| ai-disclosure | The repo-facts "contribution policy" line and any AI policy file it names, read against whether either comment in the package names the AI assistance behind it. | Treat every package here as AI-assisted work. Pass when the stated policy does not require disclosure in issue comments. That includes: no stated AI policy at all; a policy that only asks contributors to understand, test, and take responsibility for what they submit; a policy that restricts or discourages AI-written code without asking for disclosure; and a policy that explicitly says no disclosure is asked for issue comments. A requirement that comments be written by humans in their own words and voice is a voice rule, not a disclosure rule, and it passes when the comment reads as a person's own writing. When the policy does require AI use to be disclosed in comments, or in submissions of any form, pass only if the claim comment or the repro report names the assistance, and fail when neither does. | required |
| control-run-present | The repro report's artifacts, looking for a second run alongside the failing one. | Pass when the report shows a contrasting run that isolates the trigger: the same command without the triggering flag, input, or setting, or the working case the issue names. A control is what turns an artifact into a demonstration, but plenty of ready reports do not need one. | preferred |
| next-step-named | The claim comment's closing intent. | Pass when the claim names a specific place it will look next, such as a file, a function, a module, or a linked upstream thread, rather than a general promise to investigate. | preferred |

## Verdict rule

- **accept** (ready to post) if and only if every `required` check
  grades `pass`.
- **reject** (hold) as soon as any one `required` check grades `fail`.
  One required failure holds the package; there is no score to average,
  and a strong report does not buy off a boilerplate claim comment any
  more than a careful claim comment rescues an empty report.
- **`unclear` on a required check counts as `fail`.** Proof that cannot
  be verified from the package is not proof that is ready to post.
- **One exception, for live claim-only drafts.** When the package is a
  claim comment with no repro report yet, the checks whose evidence is
  the repro report grade `unclear` with evidence `not yet applicable:
  claim-only draft`, and they are left out of the verdict rule
  entirely. Those checks are `env-recorded`, `steps-rerunnable`,
  `artifact-shows-issue-behavior`, `deviation-named`, and
  `control-run-present`. The verdict then rests on
  `claim-comment-specific`, `ai-disclosure`, and whatever part of
  `outcome-stated-honestly` the claim comment itself carries, and it
  answers only: is this claim comment ready to post?
- **`preferred` checks never move a verdict.** Report their grades and
  use them to say how strong an accepted package is.
- Grade and report every check, even after the first failure, so the
  run says why the package is held and what else is wrong with it.
