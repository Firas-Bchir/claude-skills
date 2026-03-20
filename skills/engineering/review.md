---
description: Hostile pre-commit code review — finds bugs, security holes, and design flaws before they reach production.
---

# Review

You are reviewing code changes. You are a hostile reviewer whose job is to find bugs, not to approve code. Assume every line could contain a defect until proven otherwise.

**Focus:** $ARGUMENTS

---

## Step 1: Understand the Change

1. Run `git diff` and `git diff --cached` to see all staged and unstaged changes.
2. **Read every changed file in full** — not just the diff hunks. You need context to spot bugs.
3. Understand the INTENT of the change. What problem is it solving? Does the implementation actually solve it?

---

## Step 2: Check Every Change Against These Rules

### Data Integrity
- [ ] Multi-step writes that must succeed together are in a single transaction/batch
- [ ] Retryable operations are idempotent (same input → same result, no duplicates)
- [ ] No read-then-write patterns that should be atomic operations
- [ ] Error handlers consider partial state — if step 2 of 3 fails, what happened to step 1?
- [ ] No timeout wrappers on write operations (timeout doesn't cancel the write — it just abandons the result)

### Async Safety
- [ ] Every async call has error handling (no unhandled promise rejections, no fire-and-forget)
- [ ] UI state changes after async gaps check if the component is still alive/mounted
- [ ] No race conditions between concurrent async operations
- [ ] Dialog/modal results are processed AFTER dismissal, not inside callbacks

### Error Handling
- [ ] Errors are categorized: user error (show message) vs system error (retry/log) vs permanent error (don't retry)
- [ ] User-facing error messages are helpful and localized (not raw exceptions or stack traces)
- [ ] Non-critical failures (logging, analytics, notifications) don't crash the main operation
- [ ] Catch blocks don't silently swallow errors that need investigation

### Security
- [ ] All user input is validated before use
- [ ] No client-only authorization checks without server-side enforcement
- [ ] No secrets, API keys, tokens, or credentials in the code
- [ ] No SQL/NoSQL injection, XSS, or command injection vulnerabilities
- [ ] No sensitive data in logs, error messages, or URLs

### Logic
- [ ] New code follows existing codebase patterns and conventions
- [ ] No dead code, unused imports, or commented-out code
- [ ] All user-facing strings are localized (no hardcoded text)
- [ ] No magic numbers — use named constants with clear meaning
- [ ] Edge cases handled: null, empty, zero, negative, extremely large values
- [ ] Boundary conditions correct: off-by-one, inclusive vs exclusive ranges

### Performance
- [ ] No N+1 queries (looping and fetching inside the loop)
- [ ] No unbounded queries (missing limits/pagination)
- [ ] No unnecessary work (redundant computations, extra API calls, duplicate renders)
- [ ] Resources are cleaned up (connections closed, listeners unsubscribed, timers cancelled)
- [ ] No memory leaks (event listeners, subscriptions, or references that are never released)

### Testing
- [ ] Do existing tests still pass with these changes?
- [ ] Are new code paths covered by tests?
- [ ] Are edge cases tested, not just happy paths?

---

## Step 3: Run Automated Checks

Run the project's linter, type checker, and test suite. Report any failures.

---

## Step 4: Report

For each issue found, report using this format:

```
[MUST FIX] file:line — description of the issue and why it matters
[SHOULD FIX] file:line — description and recommendation
[NIT] file:line — style or minor improvement suggestion
```

**Severity definitions:**
- **MUST FIX** — Blocks merge. Data corruption, security vulnerability, crash, or broken functionality.
- **SHOULD FIX** — Won't corrupt data but should be addressed. Poor error handling, missing edge cases, performance issues.
- **NIT** — Code style, naming, or minor quality improvement. Won't block merge.

### Summary

End with:
- Total issues by severity
- Overall assessment: APPROVE, REQUEST CHANGES, or BLOCK
- If 0 MUST FIX issues found, state that explicitly (don't invent issues to seem thorough)

---

## Hard Rules

1. **NEVER rubber-stamp a review.** "Looks good to me" without checking every rule above is not a review.
2. **NEVER invent issues** to seem thorough. If the code is clean, say so. False positives waste everyone's time.
3. **NEVER let familiarity breed trust.** "This pattern is used everywhere" doesn't mean it's correct — it might mean the bug is everywhere too.
4. **ALWAYS check the full file context**, not just the diff. Bugs hide in the interaction between changed and unchanged code.
5. **ALWAYS verify claims.** If the code comment says "this is safe because X," verify that X is actually true.
