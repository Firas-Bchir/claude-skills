---
description: Technical RFC — structured proposal with problem statement, options, tradeoffs, and recommendation for team alignment.
---

# RFC

You are writing a Request for Comments (RFC) — a technical proposal that presents a problem, explores solutions, and recommends an approach. RFCs exist so teams can debate designs BEFORE they're implemented, not after. A good RFC saves weeks of rework by surfacing disagreements and blind spots early.

**Proposal:** $ARGUMENTS

---

## RFC Structure

Write the RFC using this template:

```markdown
# RFC: [Title — What This Proposes]

**Author:** [Name]
**Date:** [YYYY-MM-DD]
**Status:** Draft | In Review | Accepted | Rejected | Superseded

## 1. Problem Statement

[What problem exists today? Be specific.

Not "authentication is bad" but "users are locked out for 30 minutes
after 3 failed attempts, causing 200+ support tickets per week. The
lockout threshold was set for security but the duration punishes
legitimate users who mistype passwords."

Include data: how many users are affected, what's the cost, how often
does this happen, since when.]

## 2. Goals and Non-Goals

### Goals (what this RFC addresses)
- [Specific, measurable outcome]
- [Specific, measurable outcome]

### Non-Goals (explicitly out of scope)
- [What this RFC intentionally does NOT address, and why]
- [This section prevents scope creep and misaligned expectations]

## 3. Current State

[How does the system work today? Include enough detail that a reader
who doesn't work on this daily can understand the status quo.

Diagrams, data flow descriptions, and code references are valuable here.
Not a full architecture doc — just enough context to evaluate the proposal.]

## 4. Proposed Solution

[Describe the recommended approach in technical detail.

- What changes to the code, infrastructure, or process?
- How does data flow in the new design?
- What APIs or interfaces change?
- What does the migration path look like?

Be specific enough that an engineer could implement this from the RFC.
Not pseudo-code for every function, but clear enough that there's no
ambiguity about the approach.]

## 5. Alternatives Considered

### Alternative A: [Name]
[How it works, pros, cons, and why it was NOT chosen.

Be fair. If this was a close call, say so. If this alternative is
clearly worse, explain what disqualifies it.]

### Alternative B: [Name]
[Same format. Include "do nothing" as an alternative when applicable —
sometimes the cost of change exceeds the cost of the status quo.]

### Why the Proposed Solution Wins
[Explicitly compare the proposed solution against alternatives.
What tradeoffs are you accepting and why?]

## 6. Design Details

### [Subsection for each major component]
[Detailed design of each piece. Schema changes, API contracts,
algorithm descriptions, configuration changes.

This is where you get specific. The previous sections sell the approach;
this section proves it works.]

### Error Handling
[What happens when things go wrong? How does the system recover?
What does the user experience during failures?]

### Security Considerations
[What are the security implications? New attack surfaces?
Authentication/authorization changes? Data privacy impacts?]

### Performance Implications
[Will this be faster, slower, or neutral? At what scale?
What's the expected resource usage?]

### Observability
[How will you know this is working correctly in production?
What metrics, logs, or alerts are needed?]

## 7. Rollout Plan

[How will this be deployed?

- Can it be done incrementally or is it all-or-nothing?
- Is a feature flag needed?
- What's the rollback plan if something goes wrong?
- What's the timeline for each phase?
- Who needs to be involved at each phase?]

## 8. Open Questions

[What hasn't been decided yet?

- [Question 1 — what information is needed to decide?]
- [Question 2 — who needs to weigh in?]

This section is critical. It tells reviewers where you need help
and prevents them from wasting time on questions you've already resolved.]

## 9. References

[Links to related documents, prior art, relevant research, prior RFCs,
design docs, or external resources that informed this proposal.]
```

---

## Writing Guidelines

### Problem Statement
- Lead with the impact: who is hurt and how much.
- Include data. "Users are frustrated" is an opinion. "200 support tickets per week" is a fact.
- Don't propose the solution here. Describe the problem so clearly that the reader independently concludes "we need to fix this."

### Proposed Solution
- Write for the skeptic, not the supporter. Anticipate objections and address them.
- Be honest about what's hard. If a section of the proposal is risky or uncertain, say so. Hiding complexity builds false confidence.
- Use diagrams when words alone are confusing. Data flow diagrams, sequence diagrams, state machines — visual representations catch design flaws that text descriptions hide.

### Alternatives
- Include at least 2 genuine alternatives. Not strawmen that are obviously bad — alternatives that a reasonable engineer might prefer.
- The "do nothing" alternative is always valid. Sometimes it wins.
- If everyone already agrees on the approach, you might not need an RFC.

### Open Questions
- Don't pretend you have all the answers. The RFC process exists to fill gaps in your thinking.
- For each question, suggest who should answer it and what information would resolve it.

---

## Review Process

When the RFC is written, present it with:

1. **Who should review this?** Identify the stakeholders whose input is needed.
2. **What feedback are you looking for?** "Is this the right approach?" vs "Is the API design correct?" vs "Did I miss any edge cases?"
3. **What's the decision deadline?** RFCs that languish in review forever are worse than no RFC.
4. **What's the decision process?** Consensus? Lead decides? Voting? Define it upfront.

---

## Hard Rules

1. **NEVER write an RFC for a decision already made.** If the approach is non-negotiable, don't waste people's time pretending to solicit feedback. RFCs are for decisions that are genuinely open.

2. **NEVER write an RFC without alternatives.** A proposal with only one option is not a proposal — it's an announcement. Show that you considered other approaches.

3. **NEVER hide complexity.** If part of the proposal is hard, risky, or uncertain — say so clearly. Reviewers who discover hidden complexity lose trust in the entire proposal.

4. **NEVER write a novel.** RFCs should be as long as necessary and not one word longer. If it takes 30 minutes to read, most people won't. Aim for 10-15 minutes reading time. Use appendices for deep technical details.

5. **NEVER skip the rollout plan.** "How do we build it?" and "how do we ship it?" are equally important. A brilliant design with no deployment plan is an academic exercise.

6. **ALWAYS include the non-goals.** What you're NOT doing is as important as what you ARE doing. Non-goals prevent scope creep and set expectations.
