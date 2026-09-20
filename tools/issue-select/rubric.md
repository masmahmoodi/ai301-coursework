# Rubric: is this a good first issue?

Five required checks, one per failure family that kills first
contributions (maintainer alive, repo in use, scope fits a newcomer,
nobody else is on it, the project allows how I work), plus three
preferred checks that only rank the issues the required ones accept.

Every recency threshold below is measured against the bundle's
`captured:` date in eval mode, and against today in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| maintainer-alive | Repo facts: the dated author list under "last 5 default-branch commits"; the "maintainer first-response sample" lines (each gives an opened date plus days-to-first-maintainer-comment, so the response date is computable); the author_association tag on each comment in the Comments section. Live mode: the same signals via references/evidence-guide.md Family 1. | Pass if either (a) at least one of the last 5 default-branch commits is dated within 180 days AND is authored by a human account, or is a project bot commit whose message merges a named human's pull request, or (b) an Owner, Member or Collaborator comment dated within 180 days appears in the first-response sample or in this issue's thread. Fail when all five commits predate 180 days and no maintainer comment inside 180 days can be found in either place. Automated commits with no human PR behind them (dependency bumps, autoupdate, leaderboard refreshes) do not count on their own. | required |
| repo-in-use | Repo facts: the "archived:" flag on the repo line, the "last push to any branch" date, the "latest release" line. Live mode: the archive banner, the front-page commit date, the Releases box. | Pass if the repo line reads archived: no AND the last push to any branch is within 180 days. Fail if archived: yes, because a read-only repo can merge nothing, or if the last push is older than 180 days. A repo with no published release still passes: release recency is scored in the preferred adoption check instead, because young and app-style repos ship from the default branch and never tag. | required |
| newcomer-scope | This issue's title, body, labels and opener author_association; the Comments section; the Repo facts "linked PRs:" entry with each PR's state. | Pass only if all four hold. (1) One outcome: the issue asks for a single defect fixed or a single addition made, not a set of separate work items. A self-described mega, umbrella or tracking issue fails; so does a body that is mainly a list of other issue numbers, and so does an open-ended invitation to keep sending PRs of any size across the codebase. (2) Done is definable from the evidence alone: the body or a maintainer comment names target files or areas, acceptance criteria, reproduction plus expected behaviour, or a diagnosed cause. (3) The design is settled: no maintainer disagreement about what to build is still open in the thread, and the issue does not show BOTH a long thread (15 or more comments) AND one or more closed-unmerged linked PRs, which together mean people have tried and bounced off. (4) If the ask is a new user-facing feature, rather than a bug fix or a docs change, a maintainer endorsed it: the opener carries Owner, Member or Collaborator, or the issue carries a good-first-issue, help-wanted or accepted label, or a maintainer said in the thread that the change should be made. An unendorsed feature wish hides a product decision a newcomer cannot make. Counting notes: one outcome that edits several files, or several sections of one doc set, is still one outcome; several instances of the same defect inside one component is still one outcome; a maintainer listing suspected causes or implementation suggestions for one defect is diagnosis, not several work items; a two-line body from a maintainer or collaborator is bounded when it names the broken behaviour. A pure usage or support question ("how do I get this to work") fails. | required |
| unclaimed | Repo facts: "this issue: assignees:" and "linked PRs:" with per-PR state. The Comments section: claim wording, author, author_association and date. The issue's opened date and the captured date. | Fail if any of these holds: an assignee is named; a linked PR is open and does the work this issue asks for; a maintainer named one person as the one doing it or said other contributions are not wanted; or a claim comment ("working on this", "I would like to work on this", "can I pick this up") is dated within 90 days of capture and no maintainer has since released the issue. Pass otherwise. A claim older than 90 days with no open linked PR is stale and does not block, especially where a maintainer invited anyone to try. A closed-unmerged linked PR is an abandoned attempt, not a live claim. Only a maintainer releases a claim, by unassigning or by saying the issue is open again: an automated inactivity or stale-bot nudge asking the claimant to confirm is not a release, and the claim stands until the bot actually unassigns. A good-first-issue label says the issue is friendly, never that it is free. | required |
| ai-contribution-policy | Repo facts: the "contribution policy" line, including the file and section it cites. Live mode: CONTRIBUTING.md in the repo root or .github/, any contributor docs it links out to, dedicated files such as AI_POLICY.md, and the AI-disclosure checkbox in PR templates. | This course workflow is AI-assisted, so pass only if the project's stated policy does not forbid it. Pass when the evidence says there is no CONTRIBUTING.md or no stated policy, and pass when the policy sets conditions we can meet: disclose AI use, review and understand the output, test it, be able to explain every change. Fail when the policy refuses AI-generated contributions of the kind this issue calls for, in words like "we do not accept AI-generated code or documentation", or says such pull requests are closed without review. Stated silence is a pass, not an unclear: absence of a policy is evidence of no restriction. | required |
| maintainer-responsive | Repo facts: the "maintainer first-response sample" lines. | Pass if at least one sampled issue drew a first Owner, Member or Collaborator comment within 7 days. A sample where every entry reads "no maintainer comment in thread" or measures in tens of days does not pass, which says a review may be slow, not that the issue is bad. | preferred |
| newcomer-signposting | This issue's labels line and body. | Pass if the issue carries a good first issue, help wanted, easy or documentation label, or the body supplies a where-to-start pointer, an acceptance-criteria checklist, a stack trace, or a first-timer walkthrough. | preferred |
| adoption | Repo facts: the star count on the repo line and the "latest release" line. | Pass if the repo has 50 or more stars, or published a release within 365 days. | preferred |

## Verdict rule

- **accept** if and only if every `required` check grades `pass`.
- **reject** as soon as any one `required` check grades `fail`. One
  required failure is enough; there is no score to average and no
  balance of strengths that offsets it.
- **`unclear` on a required check counts as `fail`.** A first issue whose
  liveness, scope or claim state cannot be verified from the evidence is
  not a first issue worth taking. One exception, stated in the check
  itself: `ai-contribution-policy` grades `pass` when the evidence
  positively reports that no policy exists, because that is an answer,
  not a gap. It grades `unclear` (and so fails) only when the policy
  surface was never looked at.
- **`preferred` checks never move a verdict.** Report their grades, and
  on an accepted issue use them to rank it against the other accepted
  candidates: better responsiveness, better signposting and wider
  adoption all mean a faster, safer first PR.
- Grade each required check independently and report all of them, even
  after the first failure, so the run says why an issue was rejected and
  what else was wrong with it.
