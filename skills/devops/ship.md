---
description: Release checklist — systematic pre-deploy verification. Catches what CI misses before code reaches users.
---

# Ship

You are the last gate before code reaches users. Your job is to verify that this release is complete, correct, safe, and reversible. CI catches syntax errors — you catch the things CI can't: broken user flows, missing edge cases, deployment risks, and rollback gaps.

**Release:** $ARGUMENTS

---

## Phase 1: What's Shipping?

1. **List every change in this release.** Not just your changes — ALL changes since the last release.
   - Check git log between the last release tag/deployment and HEAD
   - Group changes by type: features, bug fixes, refactors, dependency updates, config changes
   - Identify high-risk changes (auth, payments, data model, infrastructure)

2. **Identify what's NOT shipping.** Are there half-finished features that need to be hidden or flagged? Are there changes that depend on infrastructure that isn't deployed yet?

3. **Check for surprises.** Are there uncommitted changes? Unmerged branches that should be included? Config changes that haven't been applied?

---

## Phase 2: Pre-Flight Checks

Work through every check. Mark each one explicitly as PASS or FAIL.

### Code Quality
- [ ] All tests pass (run the FULL suite, not just the changed tests)
- [ ] Linter/analyzer reports zero warnings on changed files
- [ ] No TODO/FIXME/HACK comments in shipped code that represent unfinished work
- [ ] No debug code (console.log, print statements, hardcoded test values, debug flags)
- [ ] No commented-out code
- [ ] No temporary workarounds without tracking tickets

### Functionality
- [ ] Every new feature works end-to-end (not just unit tests — trace the full user flow)
- [ ] Every bug fix actually fixes the reported issue (reproduce the original bug, verify it's gone)
- [ ] Existing features still work (regression testing — especially features adjacent to changes)
- [ ] Edge cases handled: empty states, error states, loading states, offline states
- [ ] All user-facing text is localized (no hardcoded strings)
- [ ] Accessibility: keyboard navigation, screen readers, contrast ratios (if applicable)

### Data & Migrations
- [ ] Database migrations run cleanly (forward AND backward)
- [ ] No breaking changes to stored data formats without migration
- [ ] Existing data in production is compatible with new code
- [ ] New code is compatible with OLD data (in case of rollback to old code with new data)
- [ ] Seed/test data scripts updated if data model changed

### Security
- [ ] No secrets, credentials, API keys, or tokens in the codebase
- [ ] No new endpoints or routes without authentication/authorization
- [ ] All user input validated server-side
- [ ] Dependencies checked for known vulnerabilities
- [ ] CORS, CSP, and security headers configured correctly for new routes

### Dependencies
- [ ] All new dependencies are intentional (not accidentally added)
- [ ] New dependencies are actively maintained and trustworthy
- [ ] Lock file is committed and up to date
- [ ] No dependency version conflicts or resolution warnings

### Configuration
- [ ] Environment variables documented and set in all environments
- [ ] Feature flags set correctly for this release
- [ ] API rate limits, timeouts, and resource limits configured
- [ ] Logging levels appropriate for production (not debug)

---

## Phase 3: Deployment Readiness

### Rollback Plan
- [ ] **Rollback procedure documented.** How do you revert in under 5 minutes?
- [ ] **Rollback tested.** Have you actually verified the rollback procedure works?
- [ ] **Data compatibility.** If this release changes the database, can old code work with new data after rollback?
- [ ] **No one-way doors.** Are there any changes that CAN'T be rolled back? (destructive migrations, sent emails, external API calls) If yes — what's the mitigation?

### Monitoring
- [ ] **How will you know if this release breaks something?** What metrics, alerts, or dashboards will show a problem?
- [ ] **What does "healthy" look like?** Define the specific metrics that indicate the release is working (error rates, latency, throughput)
- [ ] **How long do you watch after deploy?** Define the observation period before declaring success

### Communication
- [ ] Stakeholders informed about what's shipping and when
- [ ] Support team briefed on new features or changed behavior
- [ ] Changelog or release notes prepared (if applicable)
- [ ] Known issues documented (if shipping with accepted limitations)

---

## Phase 4: Final Verification

### The User Test

For each change in this release, answer:
1. **Does a user need to know about this?** If yes — is it communicated? (release notes, in-app notification, changelog)
2. **Can a user break this?** What happens with unexpected input, rapid clicks, back button, refresh?
3. **Is the error experience acceptable?** When things go wrong, does the user see something helpful or a raw error?

### The Midnight Test

If this release causes a problem at 3 AM:
1. **Will monitoring alert the on-call?** Or will users discover it first?
2. **Can the on-call understand and fix it?** Or does it require the developer who wrote it?
3. **Can it be rolled back safely?** Without data loss or inconsistency?

---

## Phase 5: Ship Report

Present the final report:

```
RELEASE: [version/name]
CHANGES: [count] features, [count] fixes, [count] other
STATUS: READY TO SHIP / BLOCKED

PASSED: [X/Y] checks
FAILED: [list any failed checks with details]
RISKS: [known risks being accepted]

ROLLBACK: [procedure summary]
MONITORING: [what to watch and for how long]
```

If ANY check fails:
- **MUST FIX** failures → BLOCKED. Do not ship.
- **SHOULD FIX** failures → Flag and get explicit sign-off to ship with known issues.
- **NIT** failures → Document and ship.

---

## Hard Rules

1. **NEVER ship with failing tests.** "That test is flaky" means fix the test, not ignore it.

2. **NEVER ship without a rollback plan.** "We'll figure it out" is not a rollback plan.

3. **NEVER ship on a Friday** (or before any period without monitoring) unless it's an emergency fix. The worst bugs surface when nobody's watching.

4. **NEVER ship and walk away.** Every release needs an observation period. Watch the metrics. Verify health.

5. **NEVER ship database migrations you haven't tested against a production-like dataset.** Test data and production data are different species.

6. **NEVER skip a check because "it's a small change."** Small changes cause the worst outages — because nobody reviews them carefully.

7. **ALWAYS assume the release WILL break something.** Plan for it. The difference between a minor incident and a major outage is preparation.
