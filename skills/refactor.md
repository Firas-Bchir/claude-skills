---
description: Safe code restructuring — reorganize code with zero behavior change, proven step-by-step with tests.
---

# Refactor

You are restructuring code to improve its design WITHOUT changing its behavior. Every refactoring step must preserve existing functionality exactly. If a single test breaks, you've gone too far.

**Target:** $ARGUMENTS

---

## Step 1: Understand the Current Code (DO NOT CHANGE CODE)

1. **Read the code being refactored in full.** Not just the function — the entire file and its callers.

2. **Map all usages.** Search the codebase for every place that calls, imports, or depends on this code. This is your compatibility contract — these usages must not break.

3. **Understand the current behavior.** What does this code do in ALL cases? Happy path, error path, edge cases. Document the behavior you must preserve.

4. **Identify existing tests.** What tests cover this code? Run them — they must all pass before AND after your refactoring. If there are no tests, write characterization tests BEFORE refactoring.

5. **Identify the smells.** What specifically is wrong with the current design? Name the problems:
   - Duplication?
   - God object/function doing too much?
   - Wrong abstraction level?
   - Unclear naming?
   - Tight coupling?
   - Complex conditionals?
   - Deep nesting?

---

## Step 2: Plan the Refactoring (DO NOT CHANGE CODE)

Present your plan:

1. **What will change** — the structural changes you'll make (rename, extract, inline, move, split, merge)
2. **What will NOT change** — the observable behavior that must be preserved
3. **The steps in order** — each step should be a single, small transformation that keeps all tests passing
4. **Risk assessment** — what could go wrong, which steps are riskiest

### Refactoring Strategy

Choose the safest approach:

- **Small steps preferred.** 10 small refactorings that each keep tests green are better than 1 big rewrite.
- **One transformation at a time.** Don't rename AND restructure in the same step. Do one, verify, then the other.
- **Outside-in when extracting.** Extract the outer layer first, then work inward.
- **Inside-out when inlining.** Inline the deepest level first, then work outward.

**STOP HERE.** Present the plan and wait for approval.

---

## Step 3: Ensure Test Coverage

Before touching production code:

1. **Run existing tests.** They must all pass. This is your baseline.
2. **Identify coverage gaps.** Are there code paths with no test coverage?
3. **Write characterization tests** for any uncovered behavior you're about to refactor. These tests document CURRENT behavior, even if that behavior is arguably wrong. The goal is to detect unintended changes.

---

## Step 4: Refactor

Execute the plan one step at a time.

### For each step:

1. **Make ONE structural change** (rename, extract, move, inline, etc.)
2. **Run all tests.** They MUST pass. If any test fails:
   - STOP immediately
   - Determine: did you change behavior (your fault) or is the test testing implementation details (test's fault)?
   - If your fault: undo and try a smaller step
   - If test's fault: update the test, but be VERY sure the test was wrong
3. **Run the linter.** Zero new warnings.
4. **Verify the change is purely structural.** Ask yourself: "If I diff the behavior before and after, is it identical?" If not, you're not refactoring — you're modifying.

### Common Refactoring Moves

- **Extract Function/Method** — Pull code into a named function. All callers get the function call.
- **Inline Function** — Replace a function call with its body. Remove the function.
- **Rename** — Change a name to better reflect its purpose. Update ALL references.
- **Move** — Relocate code to a better module/file/class. Update ALL imports.
- **Extract Variable** — Name a complex expression. Replace the expression with the name.
- **Replace Conditional with Polymorphism** — Turn if/switch into method dispatch.
- **Consolidate Duplicates** — Merge duplicated code into a shared function.

---

## Step 5: Verify

After all refactoring steps are complete:

1. **Run the full test suite.** Every test that passed before must still pass.
2. **Run the linter.** Zero new warnings.
3. **Review the diff.** Read every changed line. Does any change alter behavior? If you're not sure, it does — investigate.
4. **Check all callers.** Do the usages you mapped in Step 1 still work? Any new import paths, renamed functions, or changed signatures?
5. **Verify no dead code.** Did the refactoring leave behind unused functions, imports, or variables? Clean them up.

---

## Hard Rules

1. **NEVER change behavior during refactoring.** If you need to change behavior, do it in a SEPARATE commit AFTER the refactoring is complete. Mixing refactoring with behavior changes makes both impossible to review.

2. **NEVER refactor without tests.** If the code has no tests, write characterization tests FIRST. Refactoring without tests is rewriting with hope.

3. **NEVER make a "big bang" change.** If you can't break it into smaller steps that each keep tests green, the refactoring is too risky. Find a smaller scope.

4. **NEVER refactor code you don't fully understand.** If you can't explain every code path, you can't guarantee behavior preservation. Read more before refactoring.

5. **NEVER optimize during refactoring.** Refactoring is about structure, not performance. Optimize in a separate step with benchmarks.

6. **ALWAYS be able to revert.** If things go wrong at any step, you should be able to go back to the last known-good state instantly.
