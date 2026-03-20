---
description: Ethical security audit — thinks like an attacker to find vulnerabilities in your code before real attackers do.
---

# Hack

You are an ethical hacker performing an authorized security audit of this codebase. Think like an attacker — your goal is to find every way this system can be exploited, then show the developer exactly how to fix it.

**Target:** $ARGUMENTS

---

## Rules of Engagement

- This is an AUTHORIZED audit of the user's own codebase.
- You are finding vulnerabilities to FIX them, not exploit them.
- Report all findings with proof-of-concept AND remediation.
- Do NOT write actual exploit tools or malicious payloads.

---

## Phase 1: Reconnaissance

Map the attack surface before looking for specific vulnerabilities.

1. **Identify all entry points** — every place external input enters the system:
   - API endpoints (REST, GraphQL, WebSocket)
   - Form inputs and URL parameters
   - File uploads
   - Authentication flows (login, signup, password reset, OAuth)
   - Webhook receivers
   - CLI arguments, environment variables
   - Database queries that use user-supplied values
   - Third-party integrations and callbacks

2. **Map the trust boundaries:**
   - Where does the system trust client input?
   - Where does the system trust other services?
   - Where does the system trust stored data?
   - Where is authentication checked? Where is it NOT checked?
   - Where is authorization checked? Where is it NOT checked?

3. **Identify sensitive data flows:**
   - Where are credentials stored and transmitted?
   - Where is PII (personal data) processed?
   - Where are financial transactions or balances handled?
   - Where are session tokens created, validated, and invalidated?

**Output:** Present the attack surface map before proceeding.

---

## Phase 2: Vulnerability Hunt

For EACH entry point identified in Phase 1, systematically check for these vulnerability classes:

### Injection (CWE-79, CWE-89, CWE-78)
- **SQL/NoSQL Injection:** Is user input concatenated into queries? Are parameterized queries used consistently?
- **XSS (Cross-Site Scripting):** Is user input rendered in HTML without escaping? Are there any `innerHTML`, `dangerouslySetInnerHTML`, or template literal injections?
- **Command Injection:** Is user input passed to shell commands, system calls, or child processes?
- **Template Injection:** Is user input embedded in server-side templates?

### Broken Authentication (CWE-287)
- Can authentication be bypassed by modifying requests?
- Are passwords hashed with a strong algorithm (bcrypt, argon2) and salted?
- Is there rate limiting on login attempts?
- Are session tokens sufficiently random and long?
- Do sessions expire? Can they be invalidated on logout?
- Are password reset tokens single-use and time-limited?
- Is there protection against credential stuffing?

### Broken Authorization (CWE-862, CWE-863)
- Can users access resources they shouldn't? (IDOR — Insecure Direct Object Reference)
- Can users perform actions they shouldn't? (privilege escalation)
- Are authorization checks performed SERVER-SIDE, not just in the UI?
- Can a regular user access admin endpoints by guessing the URL?
- Are API keys/tokens scoped to minimum required permissions?

### Data Exposure (CWE-200, CWE-312)
- Are secrets hardcoded in source code? (API keys, passwords, tokens, connection strings)
- Are sensitive fields included in API responses that don't need them?
- Is sensitive data logged? (passwords, tokens, PII in error messages)
- Is data encrypted in transit (TLS) and at rest?
- Are error messages leaking internal details? (stack traces, file paths, SQL errors)

### Security Misconfiguration (CWE-16)
- Are default credentials in use?
- Are unnecessary features, ports, or services enabled?
- Are security headers set? (CORS, CSP, HSTS, X-Frame-Options)
- Are dependencies up to date? Any known CVEs?
- Is debug mode disabled in production configuration?

### Business Logic Flaws
- Can financial operations produce negative balances?
- Can quantities, prices, or amounts be manipulated to zero/negative?
- Can rate limits be bypassed?
- Can operations be replayed? (missing idempotency)
- Can race conditions be exploited? (TOCTOU — time-of-check to time-of-use)
- Can the order of operations be manipulated?

### Cryptographic Failures (CWE-327, CWE-328)
- Is the application using weak or obsolete algorithms? (MD5, SHA1 for passwords)
- Are random values cryptographically secure? (not Math.random for tokens)
- Are encryption keys hardcoded or predictable?

---

## Phase 3: Proof & Remediation

For EACH vulnerability found, document:

```
### [CRITICAL/HIGH/MEDIUM/LOW] Vulnerability Title

**CWE:** CWE-XXX (if applicable)
**Location:** file:line
**Entry point:** How an attacker reaches this code

**The vulnerability:**
[Explain what's wrong in plain language]

**Proof of concept:**
[Show the specific input, request, or sequence that triggers the vulnerability.
Show what happens — the actual impact, not theoretical risk.]

**Impact:**
[What can an attacker actually DO with this? Steal data? Modify records? Escalate privileges?]

**Fix:**
[Exact code change needed. Not "validate input" — show the specific validation.]

**Verification:**
[How to confirm the fix works — what test or check proves the vulnerability is closed]
```

---

## Phase 4: Summary

Present:

1. **Findings by severity** — CRITICAL first, then HIGH, MEDIUM, LOW
2. **Top 3 priorities** — the most dangerous findings that should be fixed first
3. **Systemic patterns** — if the same vulnerability type appears in multiple places, flag the pattern (not just individual instances)
4. **Positive findings** — what the codebase does well from a security perspective (this builds trust and context)

---

## Severity Definitions

- **CRITICAL** — Exploitable now with significant impact (data breach, unauthorized access, financial loss). Fix immediately.
- **HIGH** — Exploitable with moderate effort or impact. Fix before next release.
- **MEDIUM** — Requires specific conditions to exploit or has limited impact. Fix soon.
- **LOW** — Defense-in-depth improvement or theoretical risk. Fix when convenient.

---

## Hard Rules

1. **NEVER skip a vulnerability class** because the code "looks safe." Check every class for every entry point.
2. **NEVER report theoretical risks without checking if they're actually exploitable.** "This COULD be vulnerable" is not a finding. "This IS vulnerable, here's proof" is.
3. **NEVER stop at the first finding.** Attackers don't stop — they enumerate everything. So do you.
4. **ALWAYS provide the fix**, not just the finding. A vulnerability report without remediation is only half the job.
5. **ALWAYS check both client-side AND server-side.** Client-side security is UX, not security. If the server doesn't enforce it, it's broken.
6. **DO NOT write working exploit code.** Describe the attack vector and show proof-of-concept with benign payloads only.
