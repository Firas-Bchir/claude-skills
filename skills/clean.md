---
description: Code quality sweep — find and fix dead code, inconsistencies, and quality issues across the entire codebase.
---

# Clean

You are performing a code quality sweep of this codebase. Your goal is to find and fix issues that degrade maintainability, readability, and reliability — without changing any behavior. This is NOT refactoring (structural changes) — this is hygiene (removing rot).

**Scope:** $ARGUMENTS

---

## Phase 1: Scan

Systematically scan the target code for these categories of issues:

### Dead Code
- **Unused imports** — imported but never referenced
- **Unused variables** — declared but never read
- **Unused functions/methods** — defined but never called (search ALL callers, including tests)
- **Unused parameters** — accepted but never used in the function body
- **Unused files** — entire files with no imports from elsewhere
- **Commented-out code** — code in comments that is not documentation (it's dead, it lives in git history)
- **Unreachable code** — code after unconditional returns, breaks, or throws
- **Dead feature flags** — flags that are always true or always false
- **Deprecated code** — marked as deprecated with no migration path or timeline

### Inconsistencies
- **Naming inconsistencies** — same concept with different names (`user`/`account`/`member`, `fetch`/`get`/`load`/`retrieve`)
- **Style inconsistencies** — mixed conventions in the same codebase (camelCase vs snake_case in the same language)
- **Pattern inconsistencies** — same problem solved differently in different places without reason
- **Error handling inconsistencies** — some paths return errors, some throw, some return null for the same type of failure
- **Import style** — mixed absolute/relative imports, mixed import ordering

### Quality Issues
- **Magic numbers/strings** — literal values without names or explanation
- **Overly complex expressions** — nested ternaries, long boolean chains, deeply nested callbacks
- **Functions that are too long** — doing too many things, hard to name, hard to test
- **Deep nesting** — more than 3-4 levels of indentation (use early returns)
- **Duplicate code** — same logic copy-pasted in multiple places (3+ instances = extract)
- **Misleading names** — names that suggest something different from what the code does
- **Stale comments** — comments that describe code that has since changed
- **TODO/FIXME/HACK comments** — review each: is it still relevant? Should it be a ticket?

### Dependency Health
- **Unused dependencies** — in the package file but not imported anywhere
- **Duplicate dependencies** — same functionality from multiple libraries
- **Pinning issues** — unpinned versions that could break on update
- **Known vulnerabilities** — dependencies with published CVEs

---

## Phase 2: Classify and Prioritize

For each issue found, classify it:

```
[DEAD CODE] file:line — description (safe to remove: yes/no)
[INCONSISTENCY] file:line — description (standardize to: X)
[QUALITY] file:line — description (improvement: X)
[DEPENDENCY] package — description (action: X)
```

### Priority Order
1. **Dead code that causes confusion** — developers reading it waste time understanding code that isn't even used
2. **Inconsistencies in high-traffic files** — files that are frequently edited
3. **Quality issues in critical paths** — code that handles money, auth, data integrity
4. **Dependency issues** — security vulnerabilities first, then unused dependencies

### What NOT to Flag
- **Intentional patterns** — if something looks "inconsistent" but has a documented reason, leave it
- **Style preferences** — don't flag things the linter should catch (configure the linter instead)
- **Working code you'd write differently** — "I would have done it this way" is not a quality issue
- **Test code** — test files have different quality standards (some duplication is acceptable for clarity)

---

## Phase 3: Present Findings

**STOP HERE.** Present all findings grouped by category before making any changes.

For each finding, include:
- What it is
- Where it is (file:line)
- Why it matters (not "it's ugly" — how does it concretely hurt the codebase?)
- What the fix is
- Whether the fix could change behavior (it shouldn't — but flag any risk)

Wait for approval before making changes.

---

## Phase 4: Clean

Apply approved fixes following these rules:

1. **One category at a time.** Fix all dead code, then all inconsistencies, then all quality issues. Don't mix categories in the same pass.

2. **Run tests after each category.** If any test breaks, your "cleaning" changed behavior — undo and investigate.

3. **Run the linter after each category.** Zero new warnings.

4. **Verify dead code is actually dead.** Before removing a function, search for:
   - Direct calls
   - Reflection-based usage (string-based lookup, decorators, dependency injection)
   - Dynamic dispatch (interface implementations, callbacks, event handlers)
   - External callers (public API, CLI, scripts, cron jobs)
   - Test-only usage (some code exists only for testability — that's not "dead")

5. **When standardizing inconsistencies, follow the majority pattern.** If 8 files use pattern A and 2 use pattern B, standardize to A (unless A is objectively worse).

6. **Don't combine cleaning with other changes.** Cleaning should be a standalone operation with its own commit — not mixed into feature work.

---

## Hard Rules

1. **NEVER change behavior during cleaning.** If a quality "fix" changes what the code does, it's not cleaning — it's a bug fix or refactor. Do it separately.

2. **NEVER remove code you're not sure is dead.** If there's any doubt about whether something is used, leave it and flag it for investigation. Removing live code is worse than leaving dead code.

3. **NEVER clean code you don't understand.** If you can't explain what the code does, you can't safely determine whether it's dead, inconsistent, or low quality.

4. **NEVER let cleaning become refactoring.** Cleaning is removing rot. Refactoring is changing structure. Extracting a duplicate block into a function is refactoring, not cleaning. Removing an unused function is cleaning. Stay in scope.

5. **NEVER prioritize aesthetics over safety.** A codebase that's slightly messy but correct is infinitely better than one that's clean but broken.
