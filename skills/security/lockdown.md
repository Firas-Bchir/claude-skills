---
description: Auth & permissions audit — trace every access path, find privilege escalation, verify server-side enforcement.
---

# Lockdown

You are auditing authentication and authorization in this codebase. Your job is to verify that every protected resource is ACTUALLY protected — not just hidden behind a UI element. Client-side checks are decoration. Server-side enforcement is security.

**Scope:** $ARGUMENTS

---

## Phase 1: Map the Access Model

### 1. Identify All Roles and Privilege Levels

- What user roles exist? (admin, user, guest, service account, etc.)
- What can each role do? (CRUD on which resources?)
- Are there role hierarchies? (admin > manager > user?)
- Are there custom permissions beyond roles? (feature flags, scopes, claims?)
- Are there service-to-service auth contexts? (API keys, service tokens, internal endpoints)

### 2. Identify All Protected Resources

Every piece of data or functionality that should NOT be accessible to everyone:

- **API endpoints** — list every route/endpoint and its required auth level
- **Database records** — which records should be scoped to which users?
- **Files/uploads** — who can read/write/delete uploaded content?
- **Admin functions** — what operations are restricted to admin users?
- **User data** — can users only see/edit their own data?
- **Business logic** — are there operations restricted by plan, subscription, or role?

### 3. Map the Authentication Flow

Trace the COMPLETE auth lifecycle:

1. **Registration** — How are accounts created? What's verified? (email, phone, identity)
2. **Login** — What credentials are accepted? How are they validated?
3. **Session management** — How is auth state maintained? (JWT, session cookie, token)
4. **Token lifecycle** — How are tokens issued, refreshed, validated, and revoked?
5. **Logout** — Does it actually invalidate the session/token? (Not just clearing the client cookie)
6. **Password reset** — Is the reset token single-use? Time-limited? Properly scoped?
7. **Multi-factor** — If MFA exists, can it be bypassed?

---

## Phase 2: Verify Every Access Path

For EACH protected resource found in Phase 1, verify these checks:

### Authentication (Who are you?)

- [ ] **Is auth required?** Can this endpoint be accessed without any authentication token?
- [ ] **Is the token validated properly?** Signature check, expiration check, issuer check?
- [ ] **Is token validation done on EVERY request?** Not just on initial load — every API call?
- [ ] **Can tokens be forged or manipulated?** Is the signing key strong and secret?
- [ ] **Are expired tokens rejected?** What's the token lifetime? Is it reasonable?
- [ ] **Can authentication be replayed?** Is there replay protection (nonce, timestamp)?

### Authorization (Are you allowed?)

- [ ] **Is there a server-side permission check?** Not just a client-side `if (user.isAdmin)` — actual server enforcement?
- [ ] **Is the check on the right object?** Does the check verify the user can access THIS specific resource, not just resources of this TYPE?
- [ ] **Can users access other users' data?** Try replacing user IDs, resource IDs, or tenant IDs in requests (IDOR testing).
- [ ] **Can users escalate privileges?** Can they modify their own role, permissions, or claims?
- [ ] **Are batch/bulk operations checked item-by-item?** Or does checking one item grant access to the whole batch?
- [ ] **Are indirect access paths checked?** If user can't access /admin, can they access /api/admin/data?

### Session Security

- [ ] **Session fixation** — Can an attacker set a user's session ID before they log in?
- [ ] **Session hijacking** — Are session tokens transmitted securely? (HttpOnly, Secure, SameSite cookies)
- [ ] **Concurrent sessions** — Can the same account be logged in from multiple places? Is that intentional?
- [ ] **Session invalidation** — On logout, password change, or permission change, are all active sessions killed?

---

## Phase 3: Test Common Attack Patterns

### IDOR (Insecure Direct Object Reference)
For every endpoint that takes a resource ID:
1. Log in as User A
2. Request User B's resource by changing the ID
3. Does the server return User B's data?

### Privilege Escalation
1. Log in as a regular user
2. Attempt admin-only operations by calling admin endpoints directly
3. Attempt to modify your own role/permissions via API

### Auth Bypass
1. Access protected endpoints without a token
2. Access protected endpoints with an expired token
3. Access protected endpoints with a token from a different environment/issuer
4. Access protected endpoints with a malformed token (truncated, wrong algorithm)

### Broken Object-Level Authorization
1. Can a user in Tenant A access Tenant B's data?
2. Can a user see resources they were shared on after sharing is revoked?
3. Can a deleted/disabled user's tokens still access resources?

### Scope Manipulation
1. If tokens have scopes, can the client request broader scopes than intended?
2. Can API keys with read-only scope perform writes?
3. Are scope checks enforced server-side or just trusted from the token?

---

## Phase 4: Report

Present findings in this format:

```
### [CRITICAL/HIGH/MEDIUM/LOW] Finding Title

**Type:** Auth bypass / Privilege escalation / IDOR / Missing auth / Missing authz
**Endpoint:** [method] /path/to/endpoint
**File:** server-side handler at file:line

**The vulnerability:**
[Plain language explanation of what's wrong]

**Attack scenario:**
1. Attacker does [step 1]
2. Server responds with [step 2]
3. Attacker now has [unauthorized access]

**Impact:**
[What data or functionality is exposed]

**Current code:**
[The specific code that should check permissions but doesn't]

**Fix:**
[Exact code change or middleware/guard to add]

**Verification:**
[How to confirm the fix works — specific test to write]
```

### Summary

After all findings:

1. **Auth model assessment** — Is the overall auth architecture sound, or fundamentally flawed?
2. **Biggest gaps** — The 3 most dangerous findings
3. **Systemic issues** — Patterns that recur (e.g., "authorization checks are on 70% of endpoints but missing from 30%")
4. **Positive findings** — What's done well (proper token validation, consistent middleware usage, etc.)

### Coverage Matrix

Present a table showing auth coverage:

```
| Resource/Endpoint    | Auth Required | Auth Enforced | Authz Required | Authz Enforced |
|---------------------|---------------|---------------|----------------|----------------|
| GET /api/users/:id  | Yes           | Yes           | Own data only  | NO — IDOR      |
| POST /api/admin/... | Yes           | Yes           | Admin only     | Yes            |
| ...                 |               |               |                |                |
```

---

## Hard Rules

1. **NEVER trust client-side checks.** If a permission is checked only in the UI (hidden button, disabled link, client-side route guard), it is NOT enforced. Attackers don't use your UI.

2. **NEVER assume "obscure = secure."** Unguessable URLs, undocumented endpoints, and internal service ports are NOT access controls. If there's no auth check, it's open.

3. **NEVER accept "but you'd need to know the ID."** UUIDs are not security. Sequential IDs, leaked URLs, browser history, and referrer headers all expose IDs. Enforce authorization on every access.

4. **NEVER stop at the first layer.** Check auth at the API gateway AND the application layer AND the database layer. Defense in depth means any single layer failing shouldn't grant access.

5. **NEVER skip the "boring" endpoints.** The health check that returns system info. The debug endpoint left from development. The file upload that doesn't check file type. Attackers look where defenders don't.

6. **ALWAYS verify BOTH authentication AND authorization.** Auth confirms identity. Authz confirms permission. Many systems check "is this user logged in?" but forget "is this user ALLOWED to do this?"
