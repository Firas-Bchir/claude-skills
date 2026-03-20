---
description: Architecture Decision Record — document technical decisions with context, alternatives, and consequences so future developers understand WHY.
---

# ADR

You are writing an Architecture Decision Record (ADR). Technical decisions without recorded context become mysteries — six months from now, someone (probably you) will ask "why did we do it this way?" and nobody will remember. Your job is to capture the decision, the context, the alternatives, and the consequences so the reasoning survives beyond the conversation.

**Decision:** $ARGUMENTS

---

## Step 1: Understand the Decision

Before documenting, understand WHAT is being decided and WHY it matters.

1. **Identify the decision.** What specific technical choice is being made? Not "we need a database" but "we're choosing PostgreSQL over MongoDB for the order service."

2. **Identify the trigger.** What forced this decision now?
   - New feature requirement?
   - Performance problem?
   - Scale limitation?
   - Technical debt?
   - Team/organizational change?
   - Dependency end-of-life?

3. **Identify the stakeholders.** Who is affected? Who has opinions? Who will live with the consequences?

4. **Identify the constraints.** What limits the options?
   - Budget, timeline, team expertise
   - Existing infrastructure and integrations
   - Compliance, security, or regulatory requirements
   - Backwards compatibility requirements

---

## Step 2: Research Alternatives

A decision without alternatives is not a decision — it's a default. Document at least 2-3 options.

For EACH alternative:

1. **How it works** — brief technical description
2. **Pros** — what makes this option attractive
3. **Cons** — what makes this option risky or costly
4. **Effort** — relative implementation cost (small / medium / large)
5. **Fit** — how well it integrates with existing system and team expertise

Include the option of "do nothing" or "status quo" when applicable. Sometimes the best decision is to not change.

---

## Step 3: Write the ADR

Use this format:

```markdown
# ADR-[NUMBER]: [Title — the decision in imperative form]

## Status

[Proposed | Accepted | Deprecated | Superseded by ADR-XXX]

## Date

[YYYY-MM-DD]

## Context

[What is the situation that is forcing this decision? What problem are we
solving? What constraints exist? Write this so someone with no context
can understand WHY this decision was necessary.

Include relevant technical details, business requirements, and team
constraints. Be specific — "high traffic" means nothing, "12K requests/second
with p99 < 200ms" means something.]

## Decision

[State the decision clearly and directly.

"We will use X for Y because Z."

Not "we decided to maybe consider using X." Be definitive.]

## Alternatives Considered

### [Alternative 1]
[Brief description, pros, cons, and why it was not chosen]

### [Alternative 2]
[Brief description, pros, cons, and why it was not chosen]

### [Alternative 3 — if applicable]
[Brief description, pros, cons, and why it was not chosen]

## Consequences

### Positive
- [What becomes easier, faster, or better because of this decision]
- [What problems does this solve]

### Negative
- [What becomes harder or more costly because of this decision]
- [What new risks or limitations does this introduce]
- [What technical debt does this create — be honest]

### Neutral
- [What changes that are neither good nor bad — just different]
- [What the team needs to learn or adjust to]

## Risks and Mitigations

- **Risk:** [What could go wrong]
  **Mitigation:** [How we'll prevent or handle it]

## Follow-up Actions

- [ ] [Concrete action needed to implement this decision]
- [ ] [Technical task, documentation update, team communication, etc.]
```

---

## Writing Guidelines

### Context Section
- Write for someone joining the team a year from now. They don't know what you know today.
- Include the business/product reason, not just the technical one. "We chose X for the payments service" matters less than "We chose X because PCI compliance requires Y and our payment volume is expected to reach Z."
- Include what you TRIED before this decision, if anything. Failed experiments are valuable context.

### Decision Section
- One clear statement. Not a paragraph of hedging.
- Use active voice: "We will..." not "It was decided that..."
- Include the scope: does this apply globally, to one service, to new code only?

### Alternatives Section
- Be fair to alternatives you didn't choose. If an alternative is described as "obviously bad," you didn't think hard enough about it or you didn't need this ADR.
- Include the REAL reason an alternative was rejected, not the polite reason. "Too expensive" is more useful than "not aligned with our strategy."

### Consequences Section
- Be honest about negative consequences. Every decision has tradeoffs. An ADR with only positive consequences is either dishonest or documenting a non-decision.
- Include operational consequences: who maintains this? Who needs to learn new tools? What monitoring is needed?

---

## Where to Store ADRs

- Create an `docs/adr/` directory (or `adr/` at root) if one doesn't exist
- Name files: `NNNN-short-title.md` (e.g., `0001-use-postgresql-for-orders.md`)
- Number sequentially — numbers never get reused
- Add a `README.md` index in the ADR directory listing all ADRs with title, date, and status

---

## Hard Rules

1. **NEVER write an ADR without alternatives.** If there's truly only one option, document WHY there's only one option. That constraint is the actual decision.

2. **NEVER omit negative consequences.** Every decision has downsides. Hiding them doesn't make them disappear — it makes them surprises.

3. **NEVER write an ADR after forgetting the context.** Write it when the decision is made, not weeks later. The reasoning is freshest in the moment.

4. **NEVER change an accepted ADR.** If the decision changes, write a NEW ADR that supersedes the old one. ADRs are a log, not a living document. The history matters.

5. **NEVER use an ADR to justify a decision already made.** If the decision is already final, still document the alternatives honestly. The purpose is to record reasoning, not to sell a choice.

6. **ALWAYS keep ADRs short and scannable.** An ADR that takes 20 minutes to read won't be read. One page is ideal. Two pages maximum.
