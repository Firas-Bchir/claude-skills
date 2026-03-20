---
description: Root cause bug fixing — trace, prove, fix ALL instances, not just the symptom. Never guess, always verify.
---

# Fix

You are fixing a bug. You MUST understand the root cause before writing any fix. Fixing symptoms creates new bugs.

**Bug:** $ARGUMENTS

---

## Step 1: Reproduce (DO NOT WRITE CODE)

1. **Read the bug description carefully.** What is the expected behavior? What is the actual behavior? What is the gap?

2. **Trace the code path** that produces the bug — from the user action (or trigger) to the point of failure. Read every file in the chain. Do not skip files because they "look fine."

3. **Identify the EXACT line(s)** where the bug occurs. Not the file, not the function — the specific lines.

4. **Explain WHY the bug happens** (root cause), not just WHAT happens (symptom).
   - A symptom is: "the button doesn't work"
   - A root cause is: "the click handler reads `state.value` which is null because the async fetch hasn't resolved yet — there's no loading guard"

5. **Determine the pattern scope.** Is this a one-off bug, or does the same pattern exist elsewhere in the codebase? Search exhaustively.

---

## Step 2: Blast Radius Analysis (DO NOT WRITE CODE)

1. **Search the ENTIRE codebase** for the same bug pattern. Use thorough search — check every file, not just the obvious ones. The same mistake is almost never made only once.

2. **List every location** where this pattern exists, with file paths and line numbers.

3. **Map dependencies.** What other code depends on the buggy code? Will your fix break any callers, consumers, or downstream logic?

4. **Check test coverage.** Are there tests covering this code path? Will they need updating? Are they currently passing (hiding the bug) or failing?

---

## Step 3: Present Findings (DO NOT WRITE CODE)

Before writing any fix, present:

```
ROOT CAUSE: [Exact technical explanation — not "it's broken" but WHY it's broken]
AFFECTED FILES: [Every file with this pattern, with line numbers]
FIX APPROACH: [What will change and why this addresses the root cause]
RISK: [What could go wrong with this fix — what assumptions are you making?]
```

**STOP HERE.** Wait for the user to approve the approach before writing code.

---

## Step 4: Fix

Apply the fix following these rules:

1. **Fix the root cause, not the symptom.** If the bug is "null pointer at line 42," don't add a null check at line 42 — find out WHY it's null and fix THAT.

2. **Fix ALL instances.** If you found the same pattern in 5 files, fix all 5. Not just the one that was reported.

3. **Smallest correct change.** Don't refactor, don't "improve" surrounding code, don't add features. Fix the bug and nothing else.

4. **Preserve existing behavior** for all non-buggy code paths. Your fix should change the broken behavior without affecting anything that was working.

5. **Run the linter and tests** after applying the fix. Zero new warnings. Zero new failures. If existing tests fail, determine whether the test was wrong (testing buggy behavior) or your fix is wrong.

---

## Step 5: Verify the Fix

For every changed file, trace through these scenarios:

- [ ] **Happy path** — Does normal operation still work correctly?
- [ ] **The original bug** — Is the reported bug actually fixed? Walk through the exact reproduction steps.
- [ ] **Error path** — Do failures still result in clean, recoverable states?
- [ ] **Boundary conditions** — Empty input, null values, concurrent access, timeout, large data.
- [ ] **Regression check** — Could this fix reintroduce a previously fixed bug or break an adjacent feature?

---

## Hard Rules

1. **NEVER guess at the root cause.** Trace the code and PROVE it. If you can't explain the exact sequence of events that causes the bug, you don't understand it yet.

2. **NEVER fix only the reported instance.** Search the codebase for every occurrence of the same pattern.

3. **NEVER "fix" by swallowing errors.** Adding `try { } catch { }` around buggy code is not a fix — it's a cover-up. The bug still happens; you just can't see it anymore.

4. **NEVER add defensive code without understanding why it's needed.** A null check is not a fix if you don't know why the value is null. It's a band-aid that hides a deeper issue.

5. **NEVER skip the verification step.** The most dangerous moment is right after you think the fix works — that's when you introduce the regression.
