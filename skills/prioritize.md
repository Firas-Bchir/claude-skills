---
description: Feature prioritization — impact vs effort analysis with data-backed reasoning and stakeholder alignment.
---

# Prioritize

You are prioritizing work. Your job is to take a list of potential projects, features, or tasks and determine the ORDER in which they should be done — based on impact, effort, risk, and strategic alignment. Prioritization is the hardest product skill because saying "yes" to one thing means saying "no" to everything else.

**Items to prioritize:** $ARGUMENTS

---

## Step 1: Gather the Candidates

List every item being considered for prioritization:

```
| # | Item | Source | Current Status |
|---|------|--------|----------------|
| 1 | [Name] | [Who requested it — user feedback, strategy, tech debt, etc.] | [Not started / In progress / Blocked] |
| 2 | [Name] | [Source] | [Status] |
```

For each item, ensure you understand:
- What it is (clear enough to estimate)
- Why it's being considered (the motivation)
- Who wants it (stakeholder, users, engineering, strategy)

---

## Step 2: Score Each Item

### Impact Assessment

For each item, estimate the impact across these dimensions:

**User Impact** (1-5):
- 5: Solves a critical pain for most users. Measurably changes a key metric.
- 4: Significantly improves experience for many users.
- 3: Noticeable improvement for a segment of users.
- 2: Minor improvement. Nice to have.
- 1: Minimal user-facing change.

**Business Impact** (1-5):
- 5: Directly drives revenue, retention, or critical business metric.
- 4: Enables revenue growth or significantly reduces cost/risk.
- 3: Supports business goals indirectly.
- 2: Marginal business benefit.
- 1: No measurable business impact.

**Strategic Alignment** (1-5):
- 5: Core to the company's stated strategy. Blocks other strategic work without it.
- 4: Strongly supports strategy. Opens new strategic opportunities.
- 3: Consistent with strategy but not critical path.
- 2: Tangentially related to strategy.
- 1: Not aligned with current strategy (could still be valuable).

### Effort Assessment

**Effort** (T-shirt sizing):
- **XS**: < 1 day. Trivial change.
- **S**: 1-3 days. Clear scope, known approach.
- **M**: 1-2 weeks. Multiple components, some unknowns.
- **L**: 2-4 weeks. Significant engineering. Multiple unknowns.
- **XL**: 1+ months. Major project. Research required. High uncertainty.

**Risk** (1-5):
- 5: Very high risk. Unproven technology, unclear requirements, many dependencies.
- 4: High risk. Significant unknowns or external dependencies.
- 3: Moderate risk. Some unknowns but manageable.
- 2: Low risk. Well-understood problem, proven approach.
- 1: Minimal risk. Straightforward, no dependencies.

### Urgency Assessment

**Time Sensitivity** (1-5):
- 5: Deadline-driven. Must ship by a specific date or the opportunity is lost.
- 4: Competitive pressure. Delay makes the problem significantly worse.
- 3: Important but not urgent. Can wait weeks without material impact.
- 2: Low urgency. Can wait months.
- 1: No time pressure. Do it when convenient.

**Dependencies** (blocking others?):
- Does this item block other high-priority work?
- Will delaying this make future work harder or more expensive?
- Is there a window of opportunity that closes?

---

## Step 3: Apply a Framework

### The Priority Matrix

Plot each item on an Impact vs Effort matrix:

```
         ┌─────────────────────────────────┐
  HIGH   │  ★ DO FIRST     │  PLAN         │
IMPACT   │  (high impact,  │  (high impact, │
         │   low effort)   │   high effort) │
         ├─────────────────┼───────────────│
  LOW    │  FILL           │  ✗ DON'T DO   │
IMPACT   │  (low impact,   │  (low impact,  │
         │   low effort)   │   high effort) │
         └─────────────────────────────────┘
              LOW EFFORT        HIGH EFFORT
```

- **DO FIRST** (high impact, low effort): These are your quick wins. Do them immediately.
- **PLAN** (high impact, high effort): These are your big bets. Plan carefully, staff appropriately.
- **FILL** (low impact, low effort): Use these to fill gaps between bigger work. Good for new team members.
- **DON'T DO** (low impact, high effort): Unless there's a compelling strategic reason, skip these.

### Weighted Scoring

Calculate a priority score:

```
Score = (User Impact × 2 + Business Impact × 2 + Strategic Alignment × 1.5 + Time Sensitivity × 1) / Effort Multiplier

Effort Multiplier: XS=1, S=2, M=4, L=8, XL=16
Risk Adjustment: Multiply effort by (1 + risk/10)
```

This is a starting point, not a formula to follow blindly. The scores reveal relative priorities — they don't make the decision.

---

## Step 4: Consider Second-Order Effects

Numbers alone don't tell the full story. For the top candidates, consider:

### Sequencing
- Does building A first make B easier or cheaper?
- Does building B first make A unnecessary?
- Is there a natural "platform" item that enables several others?

### Team Considerations
- Does the team have the skills for this right now, or does it require ramp-up?
- Can this be parallelized, or does it need the same person/team as other high-priority items?
- Is the team fatigued from similar work? Would variety help morale?

### Opportunity Cost
- What are you NOT building by choosing this?
- Is the thing you're NOT building more valuable?
- Can you do a smaller version that captures 80% of the value at 20% of the cost?

### Reversibility
- If this turns out to be wrong, how hard is it to undo?
- Irreversible decisions need more confidence. Reversible ones can be decided faster.

---

## Step 5: Present the Prioritized List

```
## Prioritized Roadmap

### Tier 1: Do Now
[Items with highest priority scores and urgency]

| Priority | Item | Impact | Effort | Rationale |
|----------|------|--------|--------|-----------|
| 1 | [Name] | [Score] | [Size] | [Why this is #1 — be specific] |
| 2 | [Name] | [Score] | [Size] | [Why this follows #1] |

### Tier 2: Do Next
[High impact items that require more planning or effort]

| Priority | Item | Impact | Effort | Rationale |
|----------|------|--------|--------|-----------|
| 3 | [Name] | [Score] | [Size] | [Rationale] |
| 4 | [Name] | [Score] | [Size] | [Rationale] |

### Tier 3: Do Later
[Important but not urgent. Plan for future quarters.]

### Tier 4: Don't Do (with reasoning)
[Items that scored too low to justify the effort — with explanation for stakeholders who requested them]

| Item | Reason Not Prioritized |
|------|----------------------|
| [Name] | [Honest reason — low impact, high effort, not aligned, etc.] |

### Key Tradeoffs
[What you're sacrificing by this ordering. What risks you're accepting.]

### Decision Points
[When to re-evaluate priorities — new data, completed items, changed strategy]
```

---

## Hard Rules

1. **NEVER prioritize without scoring.** "This feels important" is not prioritization. Score impact and effort for every item, even roughly.

2. **NEVER prioritize everything as "high priority."** If everything is P1, nothing is P1. Force-rank. Make the hard calls.

3. **NEVER hide the "don't do" list.** Stakeholders who requested deprioritized items deserve to know why, clearly and respectfully.

4. **NEVER prioritize based on who's loudest.** The most vocal stakeholder doesn't always have the most important request. Weight by impact, not by volume.

5. **NEVER set priorities once and forget.** Priorities change as you learn. Re-evaluate at regular intervals (weekly for sprints, monthly for roadmaps, quarterly for strategy).

6. **ALWAYS show your reasoning.** The prioritized list is only useful if people understand WHY items are in that order. Without reasoning, every stakeholder will challenge every decision.
