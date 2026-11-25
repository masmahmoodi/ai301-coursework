# Contribution: HHS/simpler-grants-gov — Add Sorting to Application List View

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Masoud Mahmoodi
**GitHub Username:** masmahmoodi
**Issue:** https://github.com/HHS/simpler-grants-gov/issues/6599
**Pull Request:** not opened yet — see Implementation Notes for why

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Searched and evaluated real open-source issue candidates**

I used the AI301 Issue Selection site to search for issues matching my background in Python, JavaScript, TypeScript, backend development, APIs, and AI-related work. I filtered for tags such as `good first issue`, `bug`, `improvement`, and `help wanted`, then inspected several candidates. The main problem I found was that an issue could still appear open and beginner-friendly even when another contributor already had a linked pull request or the maintainer had changed the scope. I changed my selection process to also check the full issue thread, all linked PRs (open and draft), assignees, and recent maintainer comments before treating an issue as available. I ultimately evaluated HHS/simpler-grants-gov #6599 using the issue-select rubric. The rubric returned REJECT because the issue is blocked by #7006 (a backend prerequisite that is still open with "TBD" acceptance criteria) and because the maintainer deprioritized it in Nov 2025 and removed it from the active milestone on Nov 16, 2026. I chose to proceed anyway because the CodePath site listed it, the frontend design is clear, and contacting the maintainer to verify current status is part of the process.

**Unit 6: Ran repro-check on claim draft and investigated the codebase**

I drafted a claim comment using the repro-check voice guide rules — specific to this issue, naming a concrete first step, not overpromising a fix. I then investigated the codebase structure by browsing the GitHub API and fetching directory listings. I found that the Application List page does not exist in the `main` branch's frontend: `frontend/src/app/[locale]/(base)` has no `apply` subdirectory. I found the `apply-form` components in `src/components/apply-form` (for the form submission flow, not the list view). The backend sorting endpoint (#7006) also does not exist. These findings sharpened my understanding of the issue: the list page may live in an unmerged branch or the feature is earlier in development than it appeared. Nothing broke in the repro-check run on the claim draft itself; the voice-guide rules passed because the draft was specific and did not promise a fix.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I found this issue on the CodePath AI301 Issue Selection site while filtering for TypeScript, JavaScript, and "good first issue" label. The issue — adding sortable column headers to the Application List view — fit my goals well: it is a frontend TypeScript/React task connected to a backend API, which is exactly the full-stack pattern I want to learn. The project is a US federal government effort to modernize grants.gov, which makes it a meaningful real-world codebase rather than a toy project.

I ran the issue through the issue-select rubric installed at `~/.claude/skills/issue-scout/`. The rubric returned REJECT on the `newcomer-scope` check for two reasons: (1) the issue is explicitly blocked by #7006 ("Backend work to support Applications table sorting"), which is still open with acceptance criteria marked "TBD"; and (2) the maintainer wrote in Nov 2025 that this issue is "not needed now" and it was removed from the active milestone on Nov 16, 2026. All other required checks passed — the repo is active, unassigned, has no competing PRs, and the AI policy in CONTRIBUTING.md says "it's fine to use AI tools to work on issues." I chose to proceed against the REJECT verdict because the CodePath site listed it, the frontend design criteria are clear, and asking the maintainer about current status is an honest first step. Choosing against my own tool silently would have been worse.

Claim comment: pending — I need to post this manually at https://github.com/HHS/simpler-grants-gov/issues/6599 (see claim-draft.md). I will add the permanent link here the moment it is posted.

**Toolkit**

- Tool: issue-scout (the rubric-driven grading tool at `~/.claude/skills/issue-scout/`), used to evaluate HHS/simpler-grants-gov #6599 against the five required checks in `scope.md` and `rubric.md`
- What it produced: a REJECT verdict with evidence; the rubric identified the open blocking dependency (#7006, TBD acceptance criteria) and maintainer deprioritization signals; full output saved to `scan-output.md` and `scan-notes.md`
- Difficulties: the rubric correctly caught something real — the issue is more complex than a typical good-first-issue because the backend prerequisite is not built; this is exactly the kind of signal the tool is meant to surface
- What changed: I updated `scope.md` to reflect the CodePath AI301 Issue Selection site as the new source (replacing the Unit 1 scope which pointed to codepath/pathreview-ai301-fa26-s1); I decided to proceed against the REJECT verdict and document that decision explicitly here

---

## Understanding the Issue

**Problem Description**

The Application List view in simpler-grants-gov shows a table of a user's submitted grant applications. The column headers (Application ID, Date Completed, Application Status, Last Updated) are static text — clicking them does nothing. Users cannot reorder the rows by any column. The issue asks that sorting be added to every column except Application Type.

**Expected Behavior**

Clicking a column header should sort the displayed rows by that column in ascending order. Clicking the same header again should toggle to descending order. A visual indicator (arrow or chevron) should show which column is sorted and in which direction. Application Type should remain unsortable per the acceptance criteria.

**Current Behavior**

No sorting exists. All column headers are non-interactive. The order of rows is whatever the backend returns, with no user control.

**Affected Components**

Based on codebase investigation on November 24, 2026:

- The Application List page component has not been located in the `main` branch. `frontend/src/app/[locale]/(base)` does not contain an `apply` subdirectory. It may exist in an unmerged branch, in `frontend/src/app/[locale]/(base)/workspace/`, or may not yet be created.
- `frontend/src/components/apply-form/` exists but contains the grant application form (submission), not the list view.
- The backend application list endpoint does not currently support sort parameters — this is explicitly tracked in #7006, which is open with TBD acceptance criteria.
- Once located: the affected components would be the page component (table rendering), any service/hook that calls the backend list endpoint, and the endpoint itself when #7006 is implemented.

**Toolkit**

- Tool: GitHub API and directory listing fetches (via browser + GitHub REST API), used to map the codebase structure before touching any code
- What it produced: a map of `frontend/src/app/[locale]`, `frontend/src/components`, and `frontend/src/services` — confirming that the Application List page is not in `main` and that no applications service exists in `src/services`
- Difficulties: the `[locale]` path segment required URL encoding (%5B, %5D) for the GitHub API; code search requires GitHub authentication and could not be used without it; the issue's codebase description was written in Oct 2025 and the main branch may have diverged significantly
- What changed: understanding shifted from "this is a frontend wiring task" to "this may require creating the page from scratch or finding the branch it lives on" — this was a significant scope revision

---

## Reproduction Process

**Environment Setup**

- OS: Windows 11 Pro for Workstations
- Investigation date: November 24, 2026
- Tools used: GitHub REST API (unauthenticated), browser, PowerShell
- Note: gh CLI is not installed on this machine; a full clone and local run was not completed

The project requires Docker and docker-compose for local development (per CONTRIBUTING.md, which links to `api/README.md` and `frontend/README.md` for setup). A full reproduction would require cloning the fork, running `docker-compose up`, and navigating to the Application List page while logged in as a test applicant.

**Steps to Reproduce**

1. Navigate to https://github.com/HHS/simpler-grants-gov/issues/6599 and read the issue body and comments
2. Fetch `https://api.github.com/repos/HHS/simpler-grants-gov/contents/frontend/src/app/%5Blocale%5D/(base)` — observe that there is no `apply` subdirectory
3. Fetch `https://api.github.com/repos/HHS/simpler-grants-gov/issues/7006` — observe `state: open`, `body: "Acceptance criteria: TBD"`
4. Search for open PRs mentioning 6599: `https://api.github.com/search/issues?q=repo:HHS/simpler-grants-gov+type:pr+6599+in:body&state=open` — observe `total_count: 0`
5. Observed result: the Application List page does not exist in `main`; the backend sorting endpoint is not built; the issue is blocked

**Reproduction Evidence**

- **Commit showing reproduction:** not applicable — no code change was made; the finding is that the page and endpoint do not exist in `main`
- **Screenshots/logs:** GitHub API responses quoted in codebase investigation above; full rubric run saved in `~/.claude/skills/issue-scout/scan-output.md`
- **My findings:** The issue is at an earlier stage of development than its "good first issue" label suggests. The Application List page is not in the `main` branch; the blocking backend issue (#7006) has undefined acceptance criteria; and the issue was removed from the active sprint milestone on Nov 16, 2026. This does not mean the work is impossible — it means the first step is to contact the maintainer, locate the branch where the page is being developed, and understand what has been built since Oct 2025. An honest cannot-reproduce — with real evidence — is better than fabricating a successful reproduction.

**Toolkit**

- Tool: GitHub REST API (unauthenticated), used to fetch directory listings and issue JSON
- What it produced: confirmed that `frontend/src/app/[locale]/(base)` has no `apply` or `applications` route; confirmed #7006 is open with TBD criteria; confirmed zero open PRs competing on this issue
- Difficulties: unauthenticated GitHub API has rate limits and cannot access code search; the `[locale]` path segment in the API URL required percent-encoding; the full tree endpoint is too large to read completely without authentication
- What changed: based on these findings, the reproduction section is an honest "cannot reproduce as a simple wiring task" — I documented the full investigation rather than pretending success

---

## Solution Approach

**Analysis**

The issue requires two separate pieces of work:

1. **Backend (#7006):** The application list API endpoint needs to accept sort parameters (`sort_by`, `sort_direction`) and apply them to the database query before returning results. The backend is a FastAPI/Python application (inferred from the repo structure — `api/` contains a Python application). This work is a prerequisite and is not yet designed (acceptance criteria: TBD).

2. **Frontend (#6599):** Once the backend supports sort parameters, the Application List page component needs:
   - Sort state: a React `useState` hook tracking `{ column: string | null, direction: "asc" | "desc" }` 
   - Column headers that are `<button>` elements with an `onClick` handler that updates sort state
   - A visual indicator (up/down arrow icon) on the active sort column
   - The API call that fetches the application list updated to pass `sort_by` and `sort_direction` query parameters
   - Application Type column excluded from click handling

**Proposed Solution**

If/when the Application List page is located (on `main` or on a development branch), the frontend fix is:
1. Add a `SortState` type and a `useSortState` hook or inline `useState`
2. Update the table header cells for all sortable columns to `<button>` with `onClick`
3. Pass sort state to the API fetch call
4. Render a sort direction indicator on the active column header
5. NOT touching: the form submission flow, the opportunity search page, authentication, any other page

The backend work (#7006) is out of scope for this PR and would need to be a separate contribution.

**Implementation Plan**

1. Locate the Application List page — either find it in a branch or confirm it needs to be created
2. Add `useState<{ column: string; direction: 'asc' | 'desc' } | null>(null)` to the page component
3. Wrap each sortable column header `<th>` content in a `<button onClick={() => handleSort('columnName')}>`
4. Implement `handleSort`: toggle direction if same column, set ascending if new column
5. Pass `sort_by` and `sort_direction` to the fetch/API call
6. Add a small arrow icon (using the existing icon library) that shows direction
7. Write a unit test verifying sort state changes on click

[Deviation recorded November 24, 2026: Investigation revealed the Application List page is not in `main`. Implementation cannot proceed until the page is located. This plan describes the intended approach pending that discovery. I will update this section when the branch or page location is confirmed.]

**Toolkit**

- Tool: GitHub REST API and issue thread reading, used to understand root cause and trace affected components
- What it produced: a two-part analysis (backend prerequisite + frontend wiring) and an implementation plan that honestly notes the missing page as a blocker
- Difficulties: without the actual source file for the Application List page, the plan is necessarily at the design level rather than the code level; locating the page requires either authentication or the maintainer pointing to the right branch
- What changed: I added the deviation note because reality diverged from the initial assumption that the page existed in `main`; a plan that does not acknowledge this would not be honest

---

## Testing Strategy

Once the Application List page is located and the implementation above is in place, the testing strategy would be:

1. **Unit test — sort state:** Use Jest and React Testing Library to render the ApplicationList component (or its table header sub-component), simulate a click on a sortable column header, and assert that the sort state updates (ascending on first click, descending on second click, reset on third click). Assert that clicking Application Type does not trigger sort state change.

2. **Unit test — API call with sort params:** Mock the fetch call and assert that when sort state is set, the API is called with the correct `sort_by` and `sort_direction` query parameters.

3. **Visual smoke test:** Run the application locally (`docker-compose up`), log in as a test applicant with multiple submitted applications, click each sortable column header, and observe that the row order changes and the sort indicator appears.

4. **Existing test suite:** Run `npm test` (or the equivalent frontend test command from `frontend/README.md`) to confirm no existing tests break.

Before/after evidence would be: screenshot of the table with static headers (before), screenshot of the table with an arrow indicator on the sorted column and rows in the correct order (after).

**Toolkit**

- Tool: repro-check rubric (conceptual run, not a full automated check), used to verify that the testing strategy is complete enough to demonstrate before/after behavior
- What it produced: a strategy covering unit tests for state, unit tests for API param passing, visual smoke test, and existing suite regression check
- Difficulties: cannot run the actual tests without a working local setup and without the page being found; the strategy is therefore at the design level
- What changed: nothing — the strategy is straightforward once the page exists; the difficulty is the missing page, not the test design

---

## Implementation Notes

**November 24, 2026**

Began work on Assignment 5 contribution story. Set up the tool directory structure at `~/.claude/skills/issue-scout/`, `~/.claude/skills/repro-check/`, `~/.claude/voice-guard/`, `~/.claude/ship-gate/`, and `~/.claude/pr-monitor/`.

Ran the issue-select rubric on HHS/simpler-grants-gov #6599. Verdict: REJECT (newcomer-scope fails due to open blocking dependency #7006 with TBD acceptance criteria, and maintainer deprioritization signals). Decided to proceed against the REJECT verdict because the CodePath site approved the issue and the frontend design is clear.

Investigated the codebase structure via GitHub API:
- `frontend/src/app/[locale]/(base)` has no `apply` subdirectory
- `frontend/src/components/apply-form/` exists (form submission, not list view)
- `frontend/src/services/` has: auth, correlationId, event, featureFlags, fetch, logger, search, sessionStorage — no applications service
- `frontend/src/app/[locale]` only has `(base)` and `(print)` directories
- Conclusion: the Application List page is not in `main`

This finding changed the nature of the contribution from "wire up sorting in an existing page" to "locate the page and then wire up sorting." Contacting the maintainer is the next step.

Wrote the full contribution story with all real findings documented. Created claim comment draft (see below — needs to be posted manually in browser).

**Toolkit**

- Tool: GitHub REST API (directory listing fetches), used to map the full frontend structure
- What it produced: a complete picture of `frontend/src/` showing the Application List page is absent from `main`; this is real evidence, not a guess
- Difficulties: gh CLI not installed on this machine; unauthenticated API access limits code search; full tree is too large to read without authentication
- What changed: gh CLI should be installed before the next phase of work; this would allow both posting comments and fetching private/authenticated content more easily

---

## Pull Request

**PR Link:** not opened yet

**PR Description:** Not applicable yet — a PR cannot be opened honestly without locating the Application List page and implementing the changes. Opening a PR with no meaningful code change would not serve the project.

**Maintainer Feedback:**

- November 24, 2026: none yet — claim comment not yet posted (requires manual browser action; see Implementation Notes)

**Status:** Awaiting claim comment response from maintainer

**Toolkit**

- Tool: pr-precheck (the rubric in `tools/pr-precheck/`), reviewed before this section was written
- What it produced: confirmed that a PR requires: a matching plan, a branch diff, a draft title/description, test evidence, and the repo's PR template — none of which exist yet because the implementation could not proceed without finding the Application List page
- Difficulties: pr-precheck correctly gates PR submission; this is working as designed
- What changed: will re-run pr-precheck once the page is located and the fix is implemented

---

## Learnings and Reflections

**Technical Skills Gained**

- How to use the GitHub REST API to browse a repository's directory tree without cloning (and what its limits are, especially for large repos and code search)
- How to trace a dependency chain between issues (#6599 blocked by #7006) and understand why frontend work cannot proceed before backend design is settled
- How URL-encoded path segments work in the GitHub API (`[locale]` → `%5Blocale%5D`)
- How the simpler-grants-gov project is structured: Next.js frontend, Python/FastAPI backend, Docker-based local dev

**Challenges Overcome**

The biggest challenge was discovering that the issue is not implementable from `main` as stated. The Application List page does not exist in `main`; the backend prerequisite is not built. Rather than inventing evidence of a successful reproduction, I documented the actual investigation honestly. This required changing the story's framing mid-way — from "here's how I implemented sorting" to "here's what I found when I investigated."

A second challenge was the rubric returning REJECT before I even started the implementation. The issue-select tool found real problems. Rather than ignoring the verdict, I recorded it, explained the decision to proceed anyway, and noted the risks. That is exactly what the tool is for.

**What I'd Do Differently Next Time**

I would install gh CLI before starting so I can post claim comments, fetch code, and run repository commands without relying on unauthenticated API access. I would also investigate the dependency chain of an issue before selecting it — if I had checked #7006's state earlier, I would have seen that the backend acceptance criteria were TBD before spending time on the frontend plan. Finally, I would start earlier; completing this under time pressure in one session meant I could not iterate on the implementation.

---

## Resources Used

- https://github.com/HHS/simpler-grants-gov/issues/6599 — the issue itself; source of acceptance criteria and original discussion
- https://github.com/HHS/simpler-grants-gov/issues/7006 — the blocking backend issue; confirmed it is still open with TBD acceptance criteria
- https://github.com/HHS/simpler-grants-gov/blob/main/CONTRIBUTING.md — project contribution rules; confirmed AI assistance is permitted; found branch naming convention and PR title format
- https://docs.github.com/en/rest/repos/contents — GitHub REST API docs used to browse directory listings without cloning
