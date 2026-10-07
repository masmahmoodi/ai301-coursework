# Procedure: how this skill grades a plan package

## Read order

Read in this order before grading any check:

1. **Repo facts block first.** Note the AI disclosure policy, if any. A stated disclosure requirement affects thread-aware gate B; record it as a fact now so you do not have to re-read later.

2. **Issue statement.** Note what the issue actually asks for: the reported symptom, the expected behavior, and any scope the reporter or thread has already settled. This is the reference the plan's scope must stay inside.

3. **Thread highlights.** Note every comment from an owner, member, or contributor. Flag any comment that names a specific file, code location, or patched artifact: that is explicit maintainer direction for the thread-aware check. If no such comment exists, note "no explicit direction".

4. **Repro evidence block.** This is the most important read. Note:
   - What the evidence demonstrates the cause is (what fails, what does not).
   - Every control run and what it rules out. A control run that shows X still happening with Y removed means Y is not the cause.
   - The exact error message, artifact, or observable failure the evidence pins down.
   Record the pinned cause and what was ruled out. Do not move on until you have written down what the repro evidence proves and what it disproves.

5. **Candidate plan.** Now read the plan against what you noted in steps 2–4.

6. **Candidate plan comment.** Read last, against the thread highlights (for direction engagement) and repo-facts AI policy.

The order matters: reading the repro evidence before the plan prevents anchoring on the plan's diagnosis when judging whether it is grounded.

## Evidence gathering

For each check, here is what to gather and where:

**diagnosis-grounded:** Pull the plan's stated cause (usually in a "Diagnosis" or "Problem statement" section, or the opening paragraph). Compare it word-by-word against the repro evidence's control runs and artifacts. Write down: (a) what cause the plan claims, (b) what the repro evidence shows is and is not the cause.

**scope-bounded:** List every distinct change the plan proposes. Count how many separate problems or systems it touches. A single targeted fix = one item. An upgrade + a refactor + a CI addition + a new abstraction = four items. More than one unrelated item is scope creep.

**executable:** Look for: named files or code areas (a path, a function name, a module name counts; "the relevant code" does not). Look for: a stated approach that is not purely investigative. If neither is present, executable fails.

**test-decisive:** Find the test plan section. Identify the observable outcome it names. Ask: could you run a command and know whether it passed or failed from the output? If yes, pass. If the outcome is subjective, vague, or unmeasurable, fail.

**thread-aware (gate A):** Go back to the explicit-direction notes from step 3. Check whether the plan comment engages with that direction. "Engages" means the comment either follows the direction or explains why a different approach is taken — not that it must agree, but that it cannot silently ignore. If no explicit direction exists, gate A passes automatically.

**thread-aware (gate B):** Check the AI policy you noted in step 1 against the plan comment. If the policy requires disclosure and the comment has none, gate B fails.

## Check execution

Execute checks in this order: diagnosis-grounded, scope-bounded, executable, test-decisive, thread-aware.

For each check:
- State the evidence fact that decides it (one line: a direct quote or a concrete observation).
- Assign pass, fail, or unclear.
- Unclear is only valid when the evidence is genuinely absent or the pass condition cannot be evaluated from the package text. When in doubt, re-read the relevant sections. If still unclear, grade as fail per the verdict rule.

Do not grade a check without stating the evidence fact. "Looks fine" or "plan seems complete" is not a valid evidence statement.

A check may be graded without re-reading the whole package once you have recorded the relevant evidence fact during the read phase. Do not revisit parts you already read unless the fact you recorded is insufficient.

If evidence for a check is genuinely absent (e.g. the plan has no test plan section at all), grade the check as fail — the absence of evidence is a failure on the check that requires that evidence.

## Verdict assembly

After all five checks are graded:

1. Collect the grades: pass / fail / unclear for each required check.
2. Treat every unclear as fail.
3. If all five grades are pass: verdict = accept.
4. If any grade is fail (including unclear-treated-as-fail): verdict = reject.
5. In the JSON output, the evidence field for each check is the one-line fact you recorded during evidence gathering.
6. If the deciding check (the one that caused a reject) is not obvious, name it in the readable summary before the JSON block.
