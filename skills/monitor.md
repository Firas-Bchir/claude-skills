---
description: Observability architect — design what to measure, alert on, and dashboard for any system. Know before your users do.
---

# Monitor

You are designing the observability layer for a system. Your job is to ensure that when something goes wrong — and it WILL go wrong — the team knows about it before users do, can diagnose it without guessing, and can confirm when it's fixed. Good monitoring is the difference between a 5-minute fix and a 5-hour outage.

**Target:** $ARGUMENTS

---

## Phase 1: Understand the System

Before deciding what to measure, understand what matters.

### 1. What Are the User-Facing Promises?

What does the system promise its users (explicitly or implicitly)?
- **Availability:** "The service is up and responsive"
- **Correctness:** "The data returned is accurate and current"
- **Performance:** "Responses arrive within X seconds"
- **Durability:** "Data is not lost"

These become your Service Level Indicators (SLIs).

### 2. What Are the Critical Paths?

Trace the most important user journeys end-to-end:
- What does a user DO with this system? (Sign up, place an order, send a message)
- Which path, if broken, causes the most damage? (Revenue loss, data loss, user churn)
- Which dependencies can take down the whole system if they fail?

### 3. What Has Broken Before?

- Review past incidents. What failed? How was it detected? How long until detection?
- What would have caught it sooner?
- Are there known fragile areas?

---

## Phase 2: Define Metrics

### The Four Golden Signals

For every service and critical path, define these four:

#### 1. Latency
How long requests take to serve.
- **Measure:** Response time distribution (p50, p95, p99) — not just average
- **Separate:** Successful request latency from error latency (a fast error is not "good performance")
- **Break down by:** Endpoint, method, customer tier, geographic region

#### 2. Traffic
How much demand the system is handling.
- **Measure:** Requests per second, transactions per minute, concurrent users
- **Break down by:** Endpoint, request type, customer segment
- **Why it matters:** Traffic anomalies (unexpected spike or sudden drop) signal problems

#### 3. Errors
How many requests fail.
- **Measure:** Error rate as percentage of total requests
- **Categorize:** Client errors (4xx — usually user's fault) vs server errors (5xx — your fault)
- **Include:** Errors that "succeed" but return wrong data (silent failures — the hardest to catch)
- **Break down by:** Error type, endpoint, error code

#### 4. Saturation
How "full" the system is.
- **Measure:** CPU utilization, memory usage, disk space, connection pool usage, queue depth
- **Predict:** At current growth rate, when will each resource be exhausted?
- **Alert before exhaustion:** 80% is a warning. 90% is urgent. 100% is an outage you saw coming.

### Business Metrics

Technical metrics tell you the system is RUNNING. Business metrics tell you it's WORKING.

- **Conversion funnel:** Sign-ups, activations, purchases — drop in any step signals a problem
- **Revenue:** Real-time revenue tracking — a sudden drop means something is very wrong
- **User actions:** Key actions per minute — if users stop doing things, the app may be broken
- **Queue backlogs:** Jobs waiting to be processed — growing backlogs mean processing can't keep up

### Dependency Metrics

For every external dependency (database, API, cache, message queue):

- **Availability:** Is it up?
- **Latency:** How long do calls take?
- **Error rate:** How often do calls fail?
- **Connection pool:** How many connections are in use vs available?
- **Rate limits:** How close are you to the limit?

---

## Phase 3: Design Alerts

### Alert Philosophy

- **Alert on symptoms, not causes.** Alert: "API latency p95 > 2s." Don't alert: "CPU above 70%." Users feel symptoms; causes are for investigation.
- **Alert on what requires human action.** If the system self-heals (auto-restart, retry), monitor it but don't page someone at 3 AM for it.
- **Every alert must have a runbook.** If the alert fires, what should the on-call do FIRST? If you can't write a runbook, the alert isn't actionable.

### Alert Levels

```
PAGE (wake someone up):
  - Service is DOWN (error rate > X% for Y minutes)
  - Data is being CORRUPTED (integrity check failing)
  - Revenue is STOPPED (zero transactions for Z minutes)

URGENT (fix within hours):
  - Latency severely degraded (p95 > threshold for Y minutes)
  - Resource approaching exhaustion (disk > 90%, connections > 85%)
  - Error rate elevated but service functional

WARNING (investigate this week):
  - Latency trending upward
  - Error rate slightly elevated
  - Resource usage growing faster than expected
  - Certificate expiring in < 14 days

INFO (log only):
  - Deployments
  - Config changes
  - Dependency version updates
```

### For Each Alert, Define:

```
Alert: [Name]
Condition: [Exact threshold and duration — "error_rate > 5% for > 3 minutes"]
Severity: [Page / Urgent / Warning / Info]
Channel: [PagerDuty / Slack / Email / Dashboard]
Runbook:
  1. [First thing to check]
  2. [Second thing to check]
  3. [How to mitigate]
  4. [Who to escalate to if mitigation fails]
```

### Anti-Patterns to Avoid

- **Alert fatigue:** Too many alerts = no one reads them. Every alert should be actionable.
- **Flapping alerts:** Alert fires, resolves, fires, resolves. Add hysteresis (require condition to persist for N minutes).
- **Missing context:** An alert that says "error rate high" without showing which endpoint, which errors, or since when is useless.
- **No escalation:** If the alert isn't acknowledged in X minutes, it should escalate automatically.

---

## Phase 4: Design Dashboards

### Dashboard Hierarchy

**Level 1: Overview (the "glance" dashboard)**
- System status: green/yellow/red
- Key metrics: requests/sec, error rate, p95 latency
- Active alerts
- Recent deployments
- Audience: Everyone. Should answer "is the system healthy?" in 3 seconds.

**Level 2: Service dashboards (the "diagnose" dashboard)**
One per service or critical path:
- Detailed latency breakdown (by endpoint, by percentile)
- Error breakdown (by type, by endpoint)
- Resource usage (CPU, memory, connections, queues)
- Dependency health
- Audience: On-call engineers. Should answer "where is the problem?" in 30 seconds.

**Level 3: Investigation dashboards (the "deep dive" dashboard)**
- Query-level database metrics
- Individual request traces
- Log search
- Cache hit rates by key pattern
- Audience: Engineers debugging a specific issue.

### Dashboard Rules

1. **Put the most important thing in the top-left.** Eyes go there first.
2. **Use consistent time ranges.** All panels on a dashboard should show the same time window.
3. **Show comparison baselines.** "Is 150 requests/sec normal?" Add a "same time last week" line.
4. **Avoid pie charts.** Use time series, bar charts, or tables. Pie charts are nearly impossible to read accurately.
5. **Include deployment markers.** Vertical lines on time series showing when deployments happened. Correlating metrics with deployments catches 80% of issues.

---

## Phase 5: Output

Present the monitoring design:

```
## Monitoring Plan: [System Name]

### SLIs and SLOs
[What you're measuring and what "healthy" looks like]

### Metrics
[Complete list of metrics to collect, grouped by golden signals + business + dependencies]

### Alerts
[Each alert with condition, severity, channel, and runbook]

### Dashboards
[Dashboard structure: overview, service, investigation — with key panels listed]

### Implementation
[What tools/services are needed. What code changes for instrumentation. What infra for collection/storage.]

### Gaps
[What can't be monitored with current infrastructure and what's needed to close the gap]
```

---

## Hard Rules

1. **NEVER monitor without alerting.** A dashboard nobody watches is decoration. Critical metrics need alerts. Alerts need runbooks.

2. **NEVER alert without a runbook.** If the on-call can't do anything about it, don't wake them up for it.

3. **NEVER rely on averages.** Averages hide problems. p95 and p99 reveal them. A 100ms average latency might hide 10% of users waiting 5 seconds.

4. **NEVER skip business metrics.** Technical health and business health are different. The servers can be perfectly healthy while the payment flow is broken.

5. **NEVER set-and-forget.** Monitoring must evolve with the system. New features need new metrics. Changed thresholds need updated alerts. Old alerts for removed features need cleanup.

6. **ALWAYS monitor from the user's perspective.** Internal health checks that say "OK" mean nothing if users can't load the page. Synthetic monitoring (external probes that simulate real users) catches what internal metrics miss.
