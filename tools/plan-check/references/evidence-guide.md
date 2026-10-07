# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

**In an eval bundle:** the repro-evidence block is the ground truth. The plan's stated cause is usually in a section labeled "Diagnosis", "Problem statement", "Cause", or in the opening paragraph of the plan. Compare them directly.

Good diagnosis evidence looks like: the plan names the same component, code path, or failure mode that the repro evidence's artifact or control run shows failing. "The post-push refresh scope misses the branch-commits context" followed by a repro that demonstrates the color not refreshing after a push from that exact context = grounded.

Weak or contradicted diagnosis looks like: the plan names a cause (e.g. "the tokenizer is too strict") while the repro's own control run shows the same items parsing fine without the flag — ruling the tokenizer out. Or the plan asserts a cause with no connection to what the repro evidence demonstrates.

**In live mode:** the plan's diagnosis text is in `plan.md`. The repro evidence is the student's posted repro comment on the issue thread (fetched via GitHub). Compare the plan's stated cause against what the repro comment's artifacts and control runs actually showed.

## Scope

**In an eval bundle:** scope evidence lives in a "Scope", "Change", or "Proposed changes" section of the candidate plan, and in the approach section. Look at both: a narrow scope statement alongside an approach that lists five unrelated changes is still scope creep.

Good scope evidence: one in-scope line, one explicit not-in-scope line, one or two changed components. "In scope: the push completion callback. Not in scope: how push status is computed or other views' refresh behavior." The bounded fix is visible at a glance.

Weak scope evidence: a plan that says "one bounded change" in a heading but then proposes regenerating preload tarballs, upgrading containerd, introducing a new abstraction layer, surfacing errors, and adding a CI matrix — the text of the proposed changes section is the evidence, not the label.

**In live mode:** scope is in `plan.md`. There should be an explicit in-scope/not-in-scope statement. A plan without any not-in-scope line is not automatically scope-creep, but raises the bar on the approach section to be narrow enough on its own.

## Executability

**In an eval bundle:** executability evidence is in the plan's "Files", "Approach", or equivalent section. A named file path, a function name, a module name, or a class name is specific enough. "The relevant module" or "the code that handles X" is not.

Good executability evidence: the plan names `pkg/gui/controllers/sync_controller.go`, says the push completion callback adds the commits context to its post-push refresh scope, and names two commit views to check manually. A stranger can open that file and find the callback.

Missing executability evidence: "profile the git modules and optimize whatever shows up hot" — no file named, no target identified, the entire execution is deferred to the profiling result. Or: "fix it upstream or vendored, whichever is easier" — the plan cannot be started because the key decision is still open.

**In live mode:** executability is in `plan.md`'s approach or files section. Cross-check that the named files actually exist in the repo (via `gh` or the repo's file tree).

## Test plan

**In an eval bundle:** the test plan is in a "Test plan", "Testing", or "Verification" section of the candidate plan. The observable outcome is what you are looking for.

Good test plan evidence: "at step 3 of the repro, the color must flip without leaving the view" — a specific step, a specific observable check, anchored to the repro steps. Or: "`zig build test` with both fuzz-derived cases passing and the no-hyperlink control unchanged." You can run a command and know.

Weak test plan evidence: "the prompt should feel fast in big repos on Windows, and `starship timings` should look much better" — no specific threshold, no specific command, no anchor to the repro steps. Or: "tests should pass" with no connection to the behavior the repro evidence demonstrated.

A test plan that covers only new unit tests and does not re-run the repro's observable failure may still pass if those unit tests directly exercise the failing behavior shown in the evidence. But a test plan that ignores the repro's observable artifact entirely fails when a re-run would be the natural check.

**In live mode:** the test plan is in `plan.md`. It should reference the Unit 2 repro steps or a direct equivalent.

## Honesty

**In an eval bundle:** honesty evidence is in "Risks", "Unknowns", or "Open questions" sections, and in any uncertainty language in the plan or comment. A plan that states open questions it has not resolved is honest. A plan that presents a specific approach as settled when the plan text shows it is still an open decision (e.g., "optimize whichever approach the profiling turns up") is not.

Good honesty evidence: "Risk, stated: I have not yet measured the per-print cost of the generation comparison; if it shows up in the print benchmark I will move the check to the two growth-adjacent call sites only." This flags a real open question with a concrete contingency.

Missing honesty: a plan that lists no risks or unknowns on a non-trivial change, or that presents a profiling-dependent decision as if the profiling result is already known. Note: the absence of a risk section is not automatically a fail — a truly bounded, well-understood fix may have no honest risks to state.

**In live mode:** honesty is in `plan.md`'s risks/unknowns section and in the draft comment.

## Comms

**In an eval bundle:** comms evidence lives in two places: (1) the thread highlights block, for maintainer direction, and (2) the repo-facts block, for AI disclosure requirements and contribution conventions. Then read the candidate plan comment against both.

Good thread engagement: the comment either explicitly references the maintainer direction ("plan follows the direction proposed here: a page generation counter...") or explains a deliberate departure. A comment that addresses the maintainer by acknowledging their suggestion is engaged even if it takes a different path.

Missing thread engagement: a thread where the owner identified `src/tui/light_windows.go` as the culprit, posted a patched binary, and asked for testing — but the plan comment proposes documentation-only and never mentions the owner's diagnosis or patched binary. The direction is explicit; the comment ignores it.

Good AI disclosure: the comment explicitly names the tool used and the extent of AI assistance, per the repo policy.

Missing AI disclosure: the repo-facts block says "all AI usage in any form must be disclosed" and the comment contains no mention of AI assistance.

**In live mode:** comms evidence comes from reading the live issue thread (for maintainer direction) and the repo's CONTRIBUTING.md or equivalent (for disclosure requirements), then checking the draft `comment.md` against both.
