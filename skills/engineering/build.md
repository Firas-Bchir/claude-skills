---
description: Plan-first implementation workflow — understand, design, get approval, build, verify. Eliminates cowboy coding.
---

# Build

You are implementing a feature or change. Follow this workflow in exact order. Do NOT skip steps. Do NOT start writing code until Step 3 is approved.

**Task:** $ARGUMENTS

---

## Step 1: Understand (DO NOT WRITE CODE)

Before anything else, deeply understand what is being asked.

1. **Read the request carefully.** Restate it in your own words to confirm understanding.
2. **Ask clarifying questions** if ANYTHING is ambiguous. Do not assume intent. List your assumptions explicitly and ask the user to confirm.
3. **Map the blast radius.** Identify ALL files, modules, flows, APIs, and data models that will be affected — not just the obvious ones. Search the codebase thoroughly.
4. **Find existing patterns.** How is this kind of thing done elsewhere in the codebase? Follow existing conventions unless they're broken.
5. **Check for tests.** Are there existing tests covering the affected code? Will they need updating?
6. **Identify risks.** What could go wrong? What are the edge cases? What assumptions are you making?

**Output:** Present a brief summary of your understanding, the affected files, existing patterns found, and any questions.

---

## Step 2: Plan (DO NOT WRITE CODE)

Present a concrete implementation plan covering:

### Architecture
- Which files will be created vs modified?
- What is the data flow? Trace it end-to-end.
- Are there new database tables/collections/fields? What are the schemas?
- Are there new API endpoints? What are the contracts?

### Edge Cases
Answer EACH of these explicitly — not "we'll handle it" but the actual strategy:

- What if the input is empty, null, zero, negative, or extremely large?
- What if the operation is interrupted halfway (crash, timeout, network loss)?
- What if two users/processes do this simultaneously?
- What if the user retries the same operation (idempotency)?
- What if dependent data was deleted or modified by another process?
- What if the user has no permission for this operation?
- What if the external service is down or slow?

### Error Handling Strategy
- What errors can occur at each step?
- Which errors are retryable vs permanent?
- What does the user see for each error type?
- Can any error leave the system in an inconsistent state?

### Backward Compatibility
- Does this change break existing clients, APIs, or stored data?
- Is a migration needed? What's the rollback plan?
- Can this be deployed incrementally (feature flag, gradual rollout)?

### Security Considerations
- What input validation is needed?
- Are there authorization checks? Both client-side AND server-side?
- Could this be abused? (rate limiting, injection, privilege escalation)

**Output:** A numbered implementation plan with clear steps.

---

## Step 3: Get Approval

**STOP HERE.** Present the plan to the user. Wait for explicit approval before writing any code.

- If the user requests changes, update the plan and present it again.
- If the plan changes significantly during implementation, STOP and get re-approval.

---

## Step 4: Implement

Execute the approved plan exactly.

### Implementation Rules

1. **Smallest correct change.** Don't refactor surrounding code. Don't add bonus features. Don't "improve" things that weren't asked for.

2. **Follow existing patterns.** Match the codebase's style, naming, structure, and conventions. Don't introduce new patterns unless the existing ones are fundamentally broken.

3. **Every write path must be safe.**
   - Multiple writes that must succeed together → single transaction or batch.
   - Operations that can retry → must be idempotent.
   - External calls → handle timeouts and failures without corrupting state.

4. **Every user-facing operation must handle four states:**
   - Loading (show progress)
   - Success (confirm to user)
   - Error (helpful message, not raw exception)
   - Edge case (empty state, no permission, not found)

5. **No fire-and-forget.** Every async operation must be awaited with error handling. No detached promises. No unhandled rejections.

6. **Validate at boundaries.** Validate all user input. Validate all external data. Trust nothing that crosses a system boundary.

7. **No hardcoded strings** in user-facing code. Use localization/constants.

---

## Step 5: Verify

Before presenting the code as done, verify EACH of these:

### Correctness
- [ ] Trace the happy path through the code — does it produce the correct result?
- [ ] Trace the error path — does every failure mode result in a clean, recoverable state?
- [ ] Trace the edge cases from Step 2 — is each one handled?
- [ ] Are there any race conditions or timing-dependent behaviors?

### Code Quality
- [ ] Run the project's linter/analyzer — zero new warnings.
- [ ] Run existing tests — zero new failures.
- [ ] Does the code follow the project's existing patterns?
- [ ] No dead code, unused imports, or commented-out code.

### Security
- [ ] All user input is validated before use.
- [ ] Authorization is checked server-side, not just client-side.
- [ ] No secrets, keys, or credentials in the code.
- [ ] No injection vulnerabilities (SQL, XSS, command injection).

### Completeness
- [ ] Does the implementation match the approved plan?
- [ ] If the plan changed during implementation, was the user notified?
- [ ] Are there any TODO comments that should be resolved before merge?

---

## Hard Rules

These are non-negotiable. Violating any of these means the implementation is broken.

1. **NEVER skip the plan step** to "save time." The 5 minutes spent planning prevents hours of rework.
2. **NEVER add features that weren't requested.** Scope creep is a bug, not a feature.
3. **NEVER refactor surrounding code** unless it's blocking your implementation.
4. **NEVER ignore test failures.** If a test fails, either the test is wrong or your code is — figure out which.
5. **NEVER commit code with known edge cases unhandled.** Flag them explicitly if they're out of scope.
6. **NEVER assume the happy path is the only path.** Production traffic is adversarial.
