# Evidence guide: where proof lives in a reproduction package

The map my rubric reads. For every kind of proof a check names, this
file says where to find it and what good looks like when you do.

One rule sits above all five families: **the issue is the reference
text.** Every judgment here is comparative. An artifact is not good or
bad on its own; it is on-target or off-target against the failure the
issue describes. Read the issue first, and keep its symptom, its
trigger, and its stated environment in view while reading everything
else.

## Environment

**Where it lives.** In an eval bundle: the repro report usually opens
with an `Environment:` line, but the facts also turn up inside a table,
in the middle of the steps, or in the claim comment; collect them from
wherever they sit. The issue's own target is in the issue section,
often as a `Version:` and `Operating system:` line or inside the
reporter's template answers. The repo-facts block's "bug reports" line
says what that project's template asks reporters for, which is the
project's own statement of what counts as enough. In live mode: the
draft's environment section, the issue's environment block on GitHub,
and the bug-report template in `.github/ISSUE_TEMPLATE/`.

**What good looks like.** The version or build of the software under
test plus the operating system or platform, at minimum. Beyond that
minimum, the issue decides: whatever axis the issue names as the
condition of the failure has to appear. If the issue says the bug needs
a non-English browser language, the browser and its language order are
environment. If it says the VM driver, the shell, the terminal, or a
release-versus-debug build changes the failure mode, that axis is
environment too. A report with no environment record at all is the
clear fail; the harder fail is a full-looking record that is silent on
the one axis the issue turns on.

## Steps

**Where it lives.** In an eval bundle: the `Steps:` block of the repro
report, plus any shell transcript, since a transcript with a prompt and
a command is itself a step. The issue's own reproduction steps are the
reference. In live mode: the draft's steps and the issue's steps or
linked reproduction.

**What good looks like.** A reader who has never seen this package can
get to the same starting state and fire the same trigger. Three things
make that true: the commands or UI actions are concrete enough to
perform, the inputs they consume are obtainable, and the trigger the
issue names is actually among them. Inputs are obtainable when they are
shown inline, short enough to retype from the report, or publicly
reachable such as a published release, a playground link, or a file the
issue itself supplies. The reproduction that lives in a private
monorepo with an unshared config is the failure to watch for: it may be
entirely true and still prove nothing a stranger can check. So is the
run that starts the tool with defaults on an issue whose trigger is a
particular flag or driver. Terseness is not the problem; four lines can
be complete. Being unable to get there is.

## Behavior shown

**Where it lives.** In an eval bundle: the fenced output blocks, log
excerpts, and described screenshots inside the repro report, read
against the issue's own error text, exit code, panic message, or wrong
output. In live mode: the same blocks in the draft, against the issue
body on GitHub.

**What good looks like.** The artifact displays the issue's symptom,
and it is the same failure mode, not merely another failure. This is
where most bad packages actually go wrong, and they go wrong in three
recognizable ways:

- **The adjacent failure.** The command was altered in some small way,
  so the tool failed earlier and differently: a graceful argument
  validation error standing in for a panic, a compile error standing in
  for a runtime path error, an HCL syntax error standing in for a crash
  inside the decoder. Compare exit codes and error text against the
  issue's, not just the fact that something went wrong.
- **The setup artifact.** The output shows a version banner, a session
  list, or a successful launch: evidence that the software exists and
  runs, not evidence of the reported symptom.
- **The over-read artifact.** The output genuinely shows something odd
  but not the reported thing, for instance garbled escape sequences
  with the process still alive, narrated as the crash the issue
  reports.

A contrast run is the strongest version of this evidence: the same
command with the trigger removed, or the working case the issue names,
next to the failing one. It turns "this happened" into "this is what
causes it".

**The cannot-reproduce case.** A report that ran the real trigger and
did not see the symptom is showing on-target evidence, as long as the
attempt's output is displayed. What makes it proof is the artifact of
the attempt plus a plain statement that the symptom did not appear. An
honest failed attempt that names what differed from the issue's
conditions is a genuinely useful comment, and it belongs on the ready
side.

## Honesty

**Where it lives.** At the seam between two places: the report's
closing sentences, its `Actual:` line, and any assertion in the claim
comment, read against the artifacts in the same package. Honesty is
never a property of a sentence on its own; it is the fit between what
the words say and what the blocks above them show.

**What good looks like.** Every outcome statement is carried by an
artifact. A claimed reproduction has a symptom displayed. A
cannot-reproduce is stated as one, rather than implied to be a success
by hopeful wording. A root cause is either demonstrated or flagged as a
guess, with hedging words like "looks like", "suggests", or "my
hypothesis" doing honest work.

The tell for the failing version is certainty language substituting for
evidence: "100 percent reproducible", "guaranteed", "conclusively
demonstrates", "I verified this race condition", "I ran it ten times
with identical results". A repeat count proves consistency, never
target. When the confidence in the prose exceeds what the blocks show,
the prose is what is wrong. The polished, headed, table-formatted
report is not safer here; it is often the one doing this, because the
format supplies an authority the evidence has not earned.

## Comms

**Where it lives.** In an eval bundle: the candidate claim comment, and
the repo-facts block's "bug reports" and "contribution policy" lines.
In live mode: the draft claim comment, `CONTRIBUTING.md`, any
`AI_POLICY.md` or `AI_USAGE_POLICY.md` it links, and the PR and issue
templates.

**What good looks like, for the claim.** The comment could only have
been written about this issue: it names the symptom, the version seen,
the file or area it will read, or the concrete next step. And it
promises only what the writer controls. Reading what it will read is
controllable. Reporting back is controllable. A fix, a deadline, or a
guaranteed outcome is not. Boilerplate is the opposite pole, and it is
recognizable by substitution: if the comment would read the same pasted
onto an unrelated issue in an unrelated repository, it is boilerplate.
A bare plus-one with "any updates?", praise-then-assign-me, and me-too
with no stated intent all fail that test.

**What good looks like, for policy.** Read the policy for what it
actually asks, and sort it into one of three shapes:

- **No ask.** No stated AI policy, or a policy about code quality and
  contributor responsibility: understand what you submit, test it, be
  able to explain it. Nothing to disclose in a comment. This is most
  repositories.
- **A voice ask.** Comments to maintainers must be in the contributor's
  own words and voice, and AI-written comments may be hidden. This is
  about who is talking, not about declaring tools, and a comment that
  reads as a person's own writing satisfies it. Do not read it as a
  disclosure requirement.
- **A disclosure ask.** All AI usage, in any form, must be disclosed,
  naming the tool and the extent of the assistance. Here the comment
  has to say so, and silence fails. Course work is AI-assisted, so the
  question is never whether there was assistance, only whether the
  comment admits it.

The disclosure ask is rare and easy to miss precisely because the
package around it can be excellent. A flawless reproduction in a
repository that requires disclosure, with no disclosure, is not ready
to post.
