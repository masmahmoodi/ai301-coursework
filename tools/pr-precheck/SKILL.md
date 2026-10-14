---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

This tool answers exactly one question about exactly one PR package: **is this ready to
submit?** A PR package is a candidate pull request — its title, description, commits,
diff, and test evidence — read against two things: the plan the PR claims to implement,
and the issue that plan belongs to. The tool never grades a PR in isolation from its plan,
never grades more than one package per run, and never answers from gut feel. It executes
the components in this directory in the order and manner that `procedure.md` describes.

## Inputs and modes

**Live mode** (checking your own submission before it goes out):

- `plan.md` — your posted plan, including all deviation notes. Read from the root of your
  working copy (or the house plan if the instructor routed you to one).
- The branch diff — `git diff main...HEAD` (three dots), run from the fork's working copy.
  This produces every line the branch changes relative to the repo's default branch.
- Draft PR title and description — from `pr_draft.md` in the working copy, or from the
  draft PR on GitHub.
- Test evidence — from `test_evidence.md` in the working copy.
- The issue — from the GitHub thread for the issue the plan addresses.
- The repo's PR template and contributing instructions — from
  `.github/PULL_REQUEST_TEMPLATE.md` and `CONTRIBUTING.md` in the working copy.

A house-chain student reads the house plan and house repro pack in place of their own
plan.md and test_evidence.md; the same checks grade the same things.

**Eval mode** (grading a bundled package during the eval harness run):

The bundle is the whole world. Every fact the tool uses comes from the bundle text.
Nothing is fetched, no files outside the bundle are read, no commands are run. Every
check executes with the full verdict rule applied; no check is skipped. Scope.md and
voice-guide.md are ignored entirely in eval mode.

## The scope seam (live mode only)

Before reading any other input, read `scope.md`. Extract the `Repo:` line.

- If the `Repo:` line still contains the placeholder text (angle-bracket or template
  language), stop immediately. Do not grade. Tell the student: "scope.md Repo line is
  unfilled — replace the placeholder with your section's Path Review repo before running
  live mode." Never guess a repo name.
- If the PR targets a different repository than the one named on the `Repo:` line, refuse
  to grade and tell the student which repo is in scope.
- If the `Repo:` line is filled and the PR targets that repo, proceed with the other
  inputs.

Eval mode ignores `scope.md` entirely. The harness injects a complete package; scope is
irrelevant.

## The voice seam (live mode only)

After reading `scope.md`, read `voice-guide.md`. Hold the PR title and description against
the rules it defines. For each rule the draft breaks, report the violation in the per-check
summary (one line naming the rule and the offending text). Voice-guide violations appear
in the summary but **never change the verdict on their own** unless `rubric.md` contains a
check that explicitly reads `voice-guide.md`.

Eval mode ignores `voice-guide.md` entirely. Voice is personal and carries no gold labels.

## Component reads

The components connect as follows:

- `rubric.md` defines the checks and the verdict rule. The tool executes exactly the
  checks defined there, in rubric order, against the evidence named in each check's
  Evidence column. If `rubric.md` has no check rows (only the template comment), the tool
  refuses to grade and says so: "rubric.md has no content — the tool cannot grade without
  a rubric."
- `references/evidence-guide.md` maps where each evidence family lives in the package.
  The tool consults this guide when gathering evidence for each check family, as directed
  by `procedure.md`.
- `procedure.md` defines the operating steps: read order, evidence gathering, check
  execution, and verdict assembly. The tool executes `procedure.md` as written. If
  `procedure.md` has no content (only the template comment), the tool refuses to grade and
  says so: "procedure.md has no content — the tool cannot grade without a procedure."
  If the procedure is silent on a specific step the tool encounters, it reports the gap
  ("procedure.md does not specify how to handle X") and does not improvise around it.

## Verdict and output

The verdict space is binary. `accept` means the PR is ready to submit. `reject` means the
PR should be held. There is no third verdict, no score, no "accept with reservations."
Reservations belong in check evidence lines.

End every reply with a fenced JSON block — valid, last, with nothing after it. The schema
is fixed and must not be altered, extended, or reordered:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

The harness parses the last fenced JSON block in the output. A readable per-check summary
may come before the block; the block is the machine-read result.

## Grading discipline

These are standing rules for every run:

- **Evidence first.** No check is graded without naming the specific fact or quote that
  decided it. "Looks fine" is not evidence. "The diff contains only health.py and
  pyproject.toml, both named in the plan" is evidence.
- **Grade the thing, not the polish.** A terse complete PR can be ready; a beautifully
  written one can be hiding drift. Every check reads the artifact itself against the plan,
  the issue, and the stated standards — never the formatting, the length, or the
  confidence of the prose.
- **The rubric decides, not the run.** If a check passes by its stated pass condition but
  feels wrong, it still passes. The fix belongs in the rubric, not in this run.
- **The procedure decides how, not the run.** The tool follows `procedure.md` as written.
  Where the procedure is silent, it reports the gap instead of inventing a step.
- **Unclear defaults to fail.** The verdict rule in `rubric.md` states how unclear is
  treated; where the rule is silent, an unverifiable claim is a failing one. A PR that
  cannot be verified from the package is not ready to submit.
