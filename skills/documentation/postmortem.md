---
description: Blameless postmortem — document incidents with timeline, root cause, impact, and concrete action items to prevent recurrence.
---

# Postmortem

You are writing a blameless incident postmortem. The incident is over — the system is stable. Now your job is to understand what happened, why it happened, and how to prevent it from happening again. A good postmortem turns a painful incident into a lasting improvement. A bad postmortem (or no postmortem) guarantees you'll relive the same incident.

**Incident:** $ARGUMENTS

---

## Step 1: Gather Facts

Before writing anything, collect all the evidence. Postmortems written from memory are fiction.

### Evidence Sources
- **Monitoring data** — Dashboards, metrics, graphs showing the anomaly
- **Alert history** — When alerts fired, who was notified, how quickly
- **Logs** — Application logs, infrastructure logs, deployment logs
- **Communication records** — Slack/Teams messages, incident channel, status page updates
- **Deployment history** — What was deployed, when, by whom
- **Configuration changes** — Any config, feature flag, or infrastructure changes
- **Customer reports** — Support tickets, social media, direct reports
- **Code changes** — Relevant PRs, commits, diffs

### Interview Key People
- Who detected the incident?
- Who responded?
- What actions were taken and in what order?
- What information was missing that would have helped?
- What worked well?

---

## Step 2: Write the Postmortem

Use this template:

```markdown
# Postmortem: [Incident Title — descriptive, not just a date]

**Date:** [YYYY-MM-DD]
**Duration:** [Total time from start of user impact to full resolution]
**Severity:** [SEV-1/2/3/4]
**Author:** [Name]
**Reviewers:** [Names — people who were involved and can verify accuracy]

## Executive Summary

[3-5 sentences maximum. What happened, how long it lasted, who was
affected, what the impact was. A busy executive should understand the
incident from this paragraph alone.]

## Impact

**Users affected:** [Number or percentage — be specific]
**Duration of impact:** [From first user impact to full resolution]
**Financial impact:** [Revenue lost, credits issued, infrastructure cost]
**Data impact:** [Was data lost, corrupted, or exposed? If so, how much?]
**Reputational impact:** [Customer complaints, social media, press]

**What users experienced:**
[Describe exactly what a user saw/felt. "500 error on checkout" not
"service degradation." Be specific enough that a non-technical
stakeholder understands the severity.]

## Timeline

[Chronological, timestamped account of everything that happened.
Include detection, response, investigation, mitigation, and resolution.
Use 24-hour time with timezone.]

| Time (UTC) | Event |
|------------|-------|
| HH:MM | [First anomaly appears in metrics] |
| HH:MM | [Alert fires / user reports issue] |
| HH:MM | [Responder acknowledges / investigation begins] |
| HH:MM | [Key finding during investigation] |
| HH:MM | [Mitigation action taken] |
| HH:MM | [Impact reduced / partial resolution] |
| HH:MM | [Full resolution confirmed] |
| HH:MM | [Monitoring confirms system healthy] |

**Detection time:** [Time from first impact to first alert/report]
**Response time:** [Time from alert to first human investigation]
**Mitigation time:** [Time from investigation start to impact reduction]
**Resolution time:** [Time from investigation start to full fix]

## Root Cause

[Technical explanation of what went wrong and WHY.

Not "the server crashed" but "a memory leak in the connection pool
caused the server to exceed its memory limit after processing 50K
requests, triggering the OOM killer. The leak was introduced in commit
abc123 when connection cleanup was moved from a finally block to a
catch block, meaning successful connections were never returned to
the pool."

Be precise. Include code references, configuration values, and the
specific chain of events.]

### Contributing Factors

[What made this worse or more likely? There's rarely a single cause.]

- [Factor 1 — e.g., "No memory alerts were configured for this service"]
- [Factor 2 — e.g., "The change bypassed code review due to urgency"]
- [Factor 3 — e.g., "Load testing didn't cover the request pattern that triggers the leak"]

## Detection

**How was this detected?** [Alert / User report / Internal discovery / Accident]

**Should it have been detected sooner?** [Yes/No — if yes, what was missing?]

**What would ideal detection look like?** [What alert or check would have caught this within minutes?]

## Response

**What went well in the response:**
- [Specific thing that worked — fast detection, effective communication, good runbook]
- [Specific thing that worked]

**What went poorly in the response:**
- [Specific thing that didn't work — slow escalation, missing runbook, unclear ownership]
- [Specific thing that didn't work]

## Lessons Learned

### Things That Went Well
[Acknowledge what worked. This reinforces good practices and balances
the postmortem so it doesn't feel purely negative.]
- [Specific positive]
- [Specific positive]

### Things That Went Poorly
[What failed, what was missing, what was confusing. Be direct but blameless.]
- [Specific negative]
- [Specific negative]

### Where We Got Lucky
[Things that COULD have made this worse but didn't. Near-misses are
learning opportunities too.]
- [Specific lucky break — e.g., "This happened at 2 AM when traffic was low. During peak hours, impact would have been 10x worse."]

## Action Items

[Every action item must be: specific, assigned to a person, and have a due date.
"Improve monitoring" is not an action item. "Add memory usage alert for
service X with threshold Y, paging on-call — Owner: @name — Due: YYYY-MM-DD" is.]

### Prevent Recurrence
| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| [Specific fix to prevent this exact issue] | @name | YYYY-MM-DD | TODO |
| [Fix for contributing factor] | @name | YYYY-MM-DD | TODO |

### Improve Detection
| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| [New alert or monitoring] | @name | YYYY-MM-DD | TODO |
| [New test or validation] | @name | YYYY-MM-DD | TODO |

### Improve Response
| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| [Runbook update] | @name | YYYY-MM-DD | TODO |
| [Process improvement] | @name | YYYY-MM-DD | TODO |

## Appendix

[Supporting data: graphs, log snippets, relevant code, architecture
diagrams. Keep the main document readable; put the evidence here.]
```

---

## Writing Guidelines

### Blameless ≠ Accountless

- **Blameless:** "The deployment bypassed staging because the process allows it during emergencies." → Fix the process.
- **Blaming:** "Engineer X deployed without testing." → This teaches nothing and discourages transparency.
- Blameless means focusing on SYSTEMS, not PEOPLE. If a person could make this mistake, the system should prevent it.

### Be Specific

- Bad: "The service was degraded for some users."
- Good: "Checkout API returned 503 for 34% of requests from 14:23 to 15:47 UTC, affecting an estimated 12,000 attempted purchases."

### Be Honest

- If you don't know something, say "unknown" — don't guess.
- If the root cause isn't fully understood, say so and create an action item to investigate further.
- If the response was slow or confused, say so. The postmortem is only valuable if it's truthful.

### Action Items Must Be Actionable

Every action item needs:
- **What** specifically will be done
- **Who** will do it
- **When** it will be done by
- **How** completion will be verified

"Improve testing" is not an action item. "Add integration test for connection pool cleanup after successful requests" is.

---

## Hard Rules

1. **NEVER blame individuals.** Systems fail, not people. If one person's mistake can cause an outage, the system needs better guardrails — that's the finding.

2. **NEVER skip the postmortem.** "It was a small incident" or "we already know what happened" are excuses. Small incidents become big ones. Write it down.

3. **NEVER write action items without owners and deadlines.** Unowned action items don't get done. Every. Single. One. Gets a name and a date.

4. **NEVER publish without review by those involved.** The people who responded to the incident must review the postmortem for accuracy. Memory is unreliable — verify against evidence.

5. **NEVER sanitize the timeline.** The postmortem must include missteps, wrong turns, and confusion during the response. These are the most valuable parts — they reveal process gaps.

6. **ALWAYS follow up on action items.** A postmortem without completed action items is just a document about how you'll have the same incident again. Schedule a follow-up review 30 days later.
