# Procedure: how this tool grades a PR package

## Read order

Read in this order before grading any check. Note the listed items from each source before
moving to the next; you will need them during the side-by-side checks.

1. **Issue**: Read the issue body and thread. Note: the bug description, any maintainer
   direction that names a specific file, approach, or patch (not a hypothesis — an explicit
   direction). Record any AI-disclosure policy stated in the repo-facts block or
   CONTRIBUTING.md.

2. **Plan**: Read plan.md (or the plan-context block in eval mode). Note: the scope pair
   (exact files named + the boundary statement), the deviation notes (each one names what
   changed from the plan and why), and the test plan (what observable behavior to check,
   and how).

3. **PR description**: Read the title and description (Summary, Issue, Changes, Testing,
   Notes for Reviewers). Note: what it claims was changed, what testing it claims was done,
   and any disclosed shortfalls. Do not open the diff yet — reading the description first
   lets you compare its claims against the diff in the next step rather than having the diff
   anchor your reading of the description.

4. **Diff**: Run `git diff main...HEAD` (in live mode, from the fork's working copy) or read
   the diff section of the eval bundle. List every changed file path. Note: which lines
   change in each file, whether any hunk contains debug statements, commented-out code, or
   edits unrelated to the plan.

5. **Test evidence**: Read the test-evidence section (eval mode) or test_evidence.md (live
   mode). Note: the before-state artifact (what failed), the after-state artifact (what the
   fix produces), and each repo check outcome (make test-unit, make test-integration, make
   lint, make typecheck).

6. **Repo facts / PR template** (live mode): Read CONTRIBUTING.md and
   `.github/PULL_REQUEST_TEMPLATE.md`. Note every section heading the template defines and
   whether AI disclosure is required.

The order is non-negotiable: plan before diff so scope boundaries are established before
you compare the file list. Description before diff so claims are noted before the diff
anchors your reading.

## Evidence gathering

Gather evidence for each check family before executing any check. Record the gathered items
explicitly; a check run from memory of the package rather than recorded evidence is not
auditable.

**Plan fidelity (silent-drift)**:
- From the plan: extract the explicit scope pair — the exact file paths named and the
  boundary statement ("only X was changed", "not in scope: Y").
- From the deviation notes: list each deviation and what it accounts for.
- From the diff: list every changed file path.
- Side-by-side: for each file in the diff, is it in the plan's scope or covered by a
  deviation note? Record yes/no for each.
- From the description's Changes and Summary sections: list what it claims was changed.
  Compare against the diff's actual file list and changed lines. Note any mismatch
  (description claims more, or claims less, than the diff shows).

**Test evidence (not-tested)**:
- From the plan's test plan: note the named observable behavior (the specific thing to
  check in before and after states).
- From the test-evidence section: extract (a) the before-state artifact verbatim, (b) the
  after-state artifact verbatim, (c) the outcome for each required repo check.
- Verify: does the evidence exercise the code path named by the plan? (If the plan names
  `api/routes/health.py` and the evidence only runs `tests/unit/test_other.py`, that is a
  different code path.)
- Note any disclosed shortfall: if the test-evidence or Notes for Reviewers explains why
  end-to-end evidence was impossible and what was substituted, record that explanation.

**Diff quality (unreviewable)**:
- From the diff: for each hunk in each file, note: (a) lines added that are not part of
  the plan's stated fix (debug prints, commented-out blocks, unrelated edits), (b) large
  reformatting-only hunks that change whitespace or style without changing logic.
- Record each debris item with its file and line reference.

**Standards and comms (standards-wall)**:
- From the PR template (eval: repo-facts block; live: PULL_REQUEST_TEMPLATE.md): list
  every section heading.
- From the PR description: for each section, record whether it contains real content or
  only the placeholder HTML comment.
- From the repo's stated policy (CONTRIBUTING.md or repo-facts): record whether AI
  disclosure is required.
- From the PR description's Notes for Reviewers: record whether an AI disclosure statement
  is present.
- From the issue thread: record any explicit maintainer direction (named file, approach, or
  posted patch). From the PR description: record whether that direction is acknowledged.

## Check execution

Run checks in rubric order: plan-fidelity, test-evidence, diff-quality, standards-comms.

For each check:
1. State the evidence gathered above for that check family.
2. Apply the rubric's pass condition exactly as written — do not soften or tighten it.
3. Assign `pass`, `fail`, or `unclear`.
   - Use `unclear` only when the package genuinely does not contain enough information to
     decide the check (the evidence is absent, not ambiguous). An ambiguous claim the
     package does not resolve is also `unclear`.
   - Under the verdict rule, `unclear` counts as fail — do not use it as a softer `fail`.
4. State the one-line fact or quote that decided the grade. This line becomes the `evidence`
   field in the output JSON.

Once evidence is recorded in the Evidence gathering stage, a check may be graded without
re-reading the whole package — the recorded evidence is the basis for the grade.

Do not let a later check change an earlier check's grade. Each check is graded once, in
rubric order, from the gathered evidence.

If the procedure is silent on a step (a situation the procedure does not cover), report
the gap in the summary rather than improvising around it.

## Verdict assembly

After all four checks are graded:

1. Apply the verdict rule from rubric.md: accept if every required check passes; reject if
   any required check is fail or unclear.
2. If the verdict is accept: the JSON `verdict` field is `"accept"`.
3. If the verdict is reject: the JSON `verdict` field is `"reject"`. The deciding check is
   the first failing required check in rubric order (plan-fidelity before test-evidence
   before diff-quality before standards-comms). Quote that check's evidence line in its
   JSON entry.
4. Emit a short per-check summary before the JSON block (one line per check: name, grade,
   evidence). Then emit the fenced JSON block as the last thing in the reply. Nothing
   follows the JSON block.

The JSON output format is fixed by CONTRACT.md and must not be altered:

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
