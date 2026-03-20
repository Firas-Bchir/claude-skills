---
description: Feature hardening — bulletproof existing code against edge cases, failures, abuse, and the unexpected.
---

# Armor

You are hardening existing code. This feature WORKS in the happy path — your job is to make it survive everything else. Real users don't follow the happy path. Networks fail. Servers crash. Inputs are garbage. Users double-click. Attackers probe. Your job is to find every way this code can break and make it resilient.

**Target:** $ARGUMENTS

---

## Phase 1: Map the Attack Surface

Read the code and identify every place where things can go wrong.

### External Inputs
Every place the code accepts data from outside its trust boundary:
- User input (forms, URL parameters, headers, file uploads)
- API responses from other services
- Database reads (data could be corrupted, missing, or stale)
- Configuration values (could be wrong, missing, or changed at runtime)
- Environment (disk full, time zone, locale, permissions)

For each input, ask:
- What if it's **null or missing**?
- What if it's the **wrong type**?
- What if it's the **wrong format** (too long, too short, special characters, unicode)?
- What if it's **malicious** (injection payload, overflow, crafted to cause maximum damage)?
- What if it's **stale** (was valid when fetched, but changed since)?

### Failure Modes
Every operation that can fail:
- Network calls (timeout, connection refused, DNS failure, partial response)
- Database operations (query timeout, connection pool exhaustion, deadlock, constraint violation)
- File operations (permission denied, disk full, file locked, path traversal)
- External services (rate limited, deprecated endpoint, changed response format)
- Concurrency (race condition, deadlock, stale read, double execution)

For each failure, ask:
- What is the **blast radius**? Does this failure cascade to other operations?
- What **state** is the system in after the failure? (Consistent? Partially updated? Corrupted?)
- Can the operation be **retried safely**? (Is it idempotent?)
- What does the **user experience** when this fails? (Helpful error? Blank screen? Infinite spinner?)

### State Transitions
Every place where the system changes state:
- Database writes (create, update, delete)
- Cache mutations
- Session/auth state changes
- External side effects (emails sent, payments charged, notifications pushed)

For each state change, ask:
- What if the operation is **interrupted halfway**?
- What if it runs **twice** (retry, double-click, duplicate event)?
- What if **two processes** do it simultaneously?
- What if it **succeeds but the confirmation fails** (write goes through, but response times out)?

---

## Phase 2: Assess Current Defenses

For each risk identified in Phase 1, check what protection currently exists:

```
[UNPROTECTED] risk — No defense exists. Code will break.
[PARTIAL]     risk — Some defense exists but has gaps. Describe the gap.
[PROTECTED]   risk — Adequate defense exists. Verify it's correct.
```

### Common Gaps

- **Validation present but incomplete** — checks for null but not for empty string, negative number, or extreme value
- **Error handling present but generic** — catches all errors the same way, doesn't distinguish retryable from permanent
- **Timeout present but dangerous** — `.timeout()` on a write operation doesn't cancel the write, just abandons the result
- **Retry present but unsafe** — retries without idempotency check, causing duplicates
- **Auth check present but client-only** — UI hides the button, but API accepts the request
- **Concurrency "handled" with hope** — no actual locking, transactions, or atomic operations

---

## Phase 3: Present Findings

**STOP HERE.** Present all findings before writing code.

For each unprotected or partially protected risk:

```
### [HIGH/MEDIUM/LOW] Risk Title

**Location:** file:line
**Trigger:** How this risk materializes (specific scenario, not theoretical)
**Current state:** What happens now when triggered
**Impact:** What goes wrong (data corruption, crash, bad UX, security hole)
**Fix:** Specific defense to add
```

Group by severity. Prioritize:
1. Data corruption risks (unprotected writes, race conditions)
2. Security risks (missing validation, auth bypasses)
3. Crash risks (unhandled errors, null dereferences)
4. UX risks (missing loading states, unhelpful errors, infinite spinners)

Wait for approval before implementing.

---

## Phase 4: Harden

Apply defenses following these principles:

### Input Hardening
1. **Validate at the boundary.** Check inputs where they ENTER the system, not deep inside the logic.
2. **Reject early, reject clearly.** Invalid input should fail fast with a specific error — not propagate and fail mysteriously later.
3. **Whitelist over blacklist.** Define what IS valid, not what ISN'T. Blacklists always miss something.
4. **Sanitize for the destination.** Escape for HTML, parameterize for SQL, encode for URLs. Each output context needs its own treatment.

### Failure Hardening
1. **Distinguish error types.** Retryable (network blip) vs permanent (not found) vs user error (bad input) → different handling for each.
2. **Fail gracefully.** When a non-critical feature fails, the core functionality should still work. The notification system being down shouldn't prevent a purchase.
3. **Make failures visible.** Log enough to diagnose. Alert on errors that need human attention. Show users what happened and what they can do.
4. **Set timeouts on everything external.** But NEVER on write operations — timeout doesn't cancel the operation, it just abandons the result.

### State Hardening
1. **Atomic operations.** Multiple writes that must succeed together → single transaction or batch. No exceptions.
2. **Idempotent operations.** Anything that can run twice (retries, double-clicks, duplicate events) must produce the same result both times.
3. **Optimistic concurrency** where appropriate. Check that data hasn't changed between read and write.
4. **Defensive state checks.** Before modifying state, verify preconditions. Before deducting balance, verify sufficient funds — IN the same transaction.

### UX Hardening
1. **Loading states.** Every async operation needs a loading indicator.
2. **Error states.** Every failure needs a user-facing message that explains what happened and what to do.
3. **Empty states.** Every list/view needs a meaningful empty state, not a blank screen.
4. **Disable-on-submit.** Prevent double-submission of forms and actions.
5. **Optimistic updates with rollback.** If you show success before confirmation, have a plan for showing failure.

---

## Phase 5: Verify

After hardening, verify each defense:

1. **Test the happy path still works.** Hardening must not break existing functionality.
2. **Test each defense specifically.** For each risk you protected against, write a test that triggers the risk and verifies the defense works.
3. **Run the full test suite.** Zero regressions.
4. **Trace error paths.** For each new error handler, trace what the user sees and verify it's helpful.

---

## Hard Rules

1. **NEVER harden with silent error swallowing.** `try { } catch { }` that does nothing is not hardening — it's hiding problems. Every catch must either handle the error meaningfully, rethrow, or log with enough context to diagnose.

2. **NEVER add defense without understanding the risk.** A null check "just in case" is not hardening — it's superstition. Know WHY the value could be null and whether the check is the right fix.

3. **NEVER change happy-path behavior during hardening.** Hardening adds resilience to edge cases. If the happy path changes, you're not hardening — you're modifying.

4. **NEVER harden with complexity.** If the defense is harder to understand than the risk it prevents, it will introduce more bugs than it catches. Keep defenses simple.

5. **NEVER assume "this won't happen."** In production, everything that CAN happen DOES happen. If the code path exists, someone will trigger it.

6. **ALWAYS test the defense, not just the feature.** A defense you haven't tested is a defense that doesn't work. Write tests that specifically trigger the failure modes and verify the defense activates.
