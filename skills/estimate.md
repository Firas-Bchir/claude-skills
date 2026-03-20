---
description: Estimation framework — scope breakdown with risk buffers, confidence intervals, and dependency mapping. Honest timelines.
---

# Estimate

You are estimating the effort for a software project or task. Your job is to produce an HONEST estimate — not an optimistic one that makes people happy today and disappointed later. Good estimates acknowledge uncertainty, identify risks, and give stakeholders the information they need to make decisions.

**Task:** $ARGUMENTS

---

## Step 1: Decompose the Work

You cannot estimate what you don't understand. Break the work into pieces small enough to reason about.

### 1. Understand the Scope

- What specifically needs to be built?
- What are the acceptance criteria? (When is it "done"?)
- What is explicitly OUT of scope?
- What decisions haven't been made yet? (These are uncertainty that inflates the estimate.)

### 2. Break Into Work Units

Decompose until each unit is:
- **Small enough to estimate:** Can you confidently say "this takes 1-3 days"? If not, break it down further.
- **Independent enough to track:** Each unit has a clear start and end state.
- **Concrete enough to verify:** You can tell when it's done.

```
Work Unit: [Name]
Description: [What specifically gets built/changed]
Dependencies: [What must be done before this can start]
Unknowns: [What you don't know that affects this estimate]
```

### 3. Identify Hidden Work

The code is only part of the effort. Always include:

- **Research/investigation** — understanding the problem, reading code, prototyping
- **Design/planning** — architecture decisions, API design, schema design
- **Testing** — writing tests, manual testing, edge case verification
- **Code review** — getting reviewed, addressing feedback, iterating
- **Integration** — connecting with other systems, end-to-end testing
- **Documentation** — user docs, API docs, runbooks, ADRs
- **Deployment** — migration scripts, feature flags, monitoring setup
- **Bug fixing** — the bugs you'll find during development (you always find some)
- **Communication** — meetings, clarifications, status updates, demos

---

## Step 2: Estimate Each Unit

For each work unit, provide a THREE-POINT ESTIMATE:

```
Work Unit: [Name]
Best case:    [Minimum time if everything goes perfectly — no surprises]
Likely case:  [Most realistic time — normal amount of surprises]
Worst case:   [Maximum time if things go wrong — but not catastrophically]
Confidence:   [How confident are you? High/Medium/Low]
Assumptions:  [What must be true for this estimate to hold]
Risks:        [What could make this take longer]
```

### Estimation Rules

1. **Estimate in ideal days** (focused work, no interruptions) then adjust for reality. Most developers get 4-5 hours of focused work per 8-hour day.

2. **Use relative sizing for uncertainty.** If you've built something similar before, the estimate is high-confidence. If it's new territory, add uncertainty.

3. **Double your gut estimate.** The first number that comes to mind is almost always the best case. Your brain is an optimist.

4. **Estimate the WHOLE task, not just coding.** Coding is typically 30-50% of the total effort. Research, testing, review, integration, and deployment are the rest.

5. **Flag unknowns explicitly.** "I don't know how the payment API handles refunds" is valuable information. Unknown unknowns are the biggest source of estimation error.

### Confidence Levels

- **High confidence (±20%):** You've done this exact type of work before. Clear requirements. Known technology.
- **Medium confidence (±50%):** You've done similar work. Some unknowns. Familiar technology with new aspects.
- **Low confidence (±100-200%):** Significant unknowns. New technology. Unclear requirements. Research needed.

---

## Step 3: Account for Risk

### Risk Factors

For each identified risk, assess:

```
Risk: [What could go wrong]
Probability: [Low / Medium / High]
Impact on timeline: [Days added if it materializes]
Mitigation: [What can be done to reduce the risk]
```

### Common Risks to Consider

- **Unclear requirements** — spec changes mid-development (HIGH probability, HIGH impact)
- **Integration surprises** — external systems behave differently than documented
- **Technical unknowns** — the approach might not work, requiring a pivot
- **Dependency delays** — waiting on other teams, API access, infrastructure
- **Scope creep** — "while you're at it, can you also..." (EXTREMELY HIGH probability)
- **Underestimated complexity** — it's always more complex than it looks
- **Testing gaps** — hard-to-test scenarios that require manual verification
- **Environment issues** — dev/staging differences from production

### Buffer Strategy

```
Base estimate (sum of likely cases):     X days
Known risk buffer (+15-25%):             Y days  [for risks you've identified]
Unknown risk buffer (+10-20%):           Z days  [for risks you haven't identified]
───────────────────────────────────────
Total estimate:                          X + Y + Z days
```

The buffer is not padding — it's honesty about uncertainty. NEVER hide the buffer. Present it transparently: "The core work is X days. I'm adding Y days for known risks and Z days for unknowns."

---

## Step 4: Map Dependencies

```
Work Unit A ──→ Work Unit B ──→ Work Unit D
                                    ↑
Work Unit C ────────────────────────┘
```

- What's the critical path? (Longest chain of dependent tasks)
- What can be parallelized?
- What external dependencies exist? (Other teams, approvals, infrastructure)
- Where are the bottlenecks? (One person must do X before anyone else can proceed)

---

## Step 5: Present the Estimate

```
## Estimate: [Task Name]

### Summary
Total: [X - Y days] (confidence: [High/Medium/Low])
Critical path: [The sequence of tasks that determines the minimum timeline]
Biggest risk: [The single factor most likely to blow the estimate]

### Breakdown

| Work Unit | Best | Likely | Worst | Confidence | Dependencies |
|-----------|------|--------|-------|------------|--------------|
| Unit 1    | 1d   | 2d     | 4d    | High       | None         |
| Unit 2    | 2d   | 3d     | 7d    | Medium     | Unit 1       |
| Unit 3    | 1d   | 2d     | 3d    | High       | None         |
| Unit 4    | 3d   | 5d     | 10d   | Low        | Unit 2, 3    |
| Buffer    | -    | 3d     | 5d    | -          | -            |
| **Total** | 7d   | 15d    | 29d   | Medium     | -            |

### Assumptions
- [What must be true for this estimate to hold]
- [What you're assuming about requirements, dependencies, environment]

### Risks
- [Risk 1: description, probability, impact, mitigation]
- [Risk 2: description, probability, impact, mitigation]

### What Could Change This Estimate
- [If X happens, add Y days]
- [If requirement Z changes, re-estimate Unit 4]
- [If dependency D is delayed, everything after Unit 2 shifts]
```

---

## Hard Rules

1. **NEVER give a single number.** Always provide a range. "5 days" is a guess. "3-8 days with medium confidence" is an estimate.

2. **NEVER estimate without decomposing.** "The whole project will take 3 months" is a guess. Breaking into units, estimating each, and summing is an estimate.

3. **NEVER hide uncertainty.** If you don't know, say you don't know. A confident wrong estimate is worse than an honest "I need to investigate before I can estimate this part."

4. **NEVER estimate under pressure to be optimistic.** "Can you get it done by Friday?" is not a constraint on reality. If it takes 2 weeks, saying "Friday" doesn't make it take less time — it just makes Friday a disappointment.

5. **NEVER forget the non-coding work.** Testing, review, deployment, communication, and bug fixing are REAL work that takes REAL time. Include them.

6. **ALWAYS revisit estimates as you learn more.** The best time to re-estimate is after completing the first work unit. You now have real data. Update the estimate. This is not "missing the target" — it's learning.
