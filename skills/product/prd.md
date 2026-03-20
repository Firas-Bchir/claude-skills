---
description: Product spec writer — transform rough ideas into detailed requirements with user stories, edge cases, and acceptance criteria.
---

# PRD

You are writing a Product Requirements Document. Your job is to transform a rough idea into a complete specification that an engineering team can build from without ambiguity. Every question a developer would ask during implementation should be answered in this document — or explicitly flagged as an open question.

**Feature:** $ARGUMENTS

---

## Step 1: Understand the Idea

Before specifying, make sure you understand the REAL need.

1. **What problem does this solve?** Not "what does it do" but "what pain does it remove?" Who has this pain? How do they cope today?

2. **Who is the user?** Not "everyone" — the specific person or persona. What's their context, skill level, and motivation?

3. **What's the desired outcome?** After using this feature, what can the user do that they couldn't before? How does their life improve?

4. **What's the business value?** Revenue, retention, engagement, operational efficiency — why should the company build this instead of something else?

5. **What's the scope?** What's included in this version? What's explicitly deferred to future versions?

Ask clarifying questions for anything ambiguous. Assumptions lead to rework.

---

## Step 2: Write the PRD

```markdown
# PRD: [Feature Name]

**Author:** [Name]
**Date:** [YYYY-MM-DD]
**Status:** Draft | In Review | Approved | In Development

## 1. Problem Statement

[Describe the problem from the USER's perspective. Include:
- Who has this problem (specific user type/persona)
- What they're trying to do
- What goes wrong or is missing today
- How painful this is (frequency, severity, workaround cost)
- Data if available (support tickets, user feedback, analytics)]

## 2. Goals

### Primary Goal
[The ONE thing this feature must accomplish. If it does nothing else, it must do this.]

### Secondary Goals
- [Additional benefit 1]
- [Additional benefit 2]

### Non-Goals
- [What this feature explicitly does NOT do]
- [What will be deferred to future versions]
- [Adjacent problems that won't be solved here]

## 3. User Stories

[Write user stories for every distinct interaction. Use the format:]

### Story 1: [Title]
**As a** [user type]
**I want to** [action]
**So that** [benefit]

**Acceptance Criteria:**
- [ ] [Specific, testable criterion — not "works well" but "returns results within 2 seconds"]
- [ ] [Another criterion]
- [ ] [Another criterion]

**Edge Cases:**
- What if [unusual but possible scenario]? → [expected behavior]
- What if [error scenario]? → [expected behavior]
- What if [boundary condition]? → [expected behavior]

### Story 2: [Title]
[Same format]

## 4. Functional Requirements

[Detailed specification of what the system must do.]

### 4.1 [Feature Area]

| ID | Requirement | Priority | Notes |
|----|-------------|----------|-------|
| FR-1 | [Specific behavior — "System shall..."] | Must have | |
| FR-2 | [Specific behavior] | Must have | |
| FR-3 | [Specific behavior] | Should have | |
| FR-4 | [Specific behavior] | Nice to have | |

### 4.2 [Feature Area]
[Same format]

## 5. Non-Functional Requirements

### Performance
- [Response time requirements — "Search results return within X ms"]
- [Throughput requirements — "Handle X concurrent users"]
- [Data volume requirements — "Support up to X records"]

### Security
- [Authentication requirements]
- [Authorization requirements]
- [Data privacy requirements]

### Reliability
- [Availability requirements — uptime SLA]
- [Data durability requirements]
- [Error handling requirements]

### Accessibility
- [WCAG compliance level]
- [Specific accessibility requirements]

### Localization
- [Supported languages]
- [RTL support]
- [Date/number/currency formatting]

## 6. UX/UI Specification

[If wireframes or mockups exist, reference them. If not, describe:]

### User Flow
1. [Step 1 — what the user does and what they see]
2. [Step 2]
3. [Step 3]

### Screen/View Descriptions
[For each screen or state:]
- What information is displayed
- What actions are available
- What happens on each action

### States
- **Loading:** [What the user sees while data is being fetched]
- **Empty:** [What the user sees when there's no data]
- **Error:** [What the user sees when something goes wrong]
- **Success:** [Confirmation of completed action]

## 7. Data Requirements

- What data does this feature need?
- Where does it come from?
- What new data does it create?
- How long is data retained?
- Are there privacy or compliance considerations?

## 8. Dependencies

- [External systems this feature depends on]
- [Other features that must exist first]
- [Team dependencies — who else needs to build something]

## 9. Analytics & Success Metrics

### Key Metrics
- [How we'll measure if this feature is successful — specific metrics]
- [Baseline values and target values]

### Events to Track
- [User actions that should be tracked for analytics]

### Success Criteria
[After launch, what data would tell us to:]
- **Keep and iterate:** [metric thresholds]
- **Pivot:** [metric thresholds]
- **Remove:** [metric thresholds]

## 10. Open Questions

- [Unresolved question — who needs to answer it, by when]
- [Unresolved question]

## 11. Future Considerations

[What's been intentionally deferred but should be planned for:]
- [Future enhancement 1 — why deferred, when to revisit]
- [Future enhancement 2]
```

---

## Writing Guidelines

### User Stories
- One story per distinct user action. Not one giant story for the whole feature.
- Acceptance criteria must be TESTABLE. "Works correctly" is not testable. "Returns the 10 most recent items sorted by date descending" is testable.
- Edge cases are not afterthoughts. They're where bugs hide and where user frustration lives.

### Requirements
- Use "must" for mandatory, "should" for important, "may" for nice-to-have.
- Each requirement should be independently verifiable.
- Avoid compound requirements ("System must do A and B" — split into two requirements).

### Edge Cases to Always Consider
- Empty state (no data yet)
- First-time user experience
- Maximum limits (too many items, too long text, too large file)
- Permissions (what if user doesn't have access?)
- Concurrent access (two users editing the same thing)
- Offline/poor connectivity
- Undo/cancel (user changes their mind mid-action)
- International users (language, timezone, currency, RTL)

---

## Hard Rules

1. **NEVER leave requirements ambiguous.** "The system should handle large datasets" means nothing. "The system must return paginated results with a maximum of 50 items per page and support up to 1 million total records" is a requirement.

2. **NEVER skip edge cases.** Every user story needs edge cases documented. If you can't think of edge cases, you don't understand the feature well enough.

3. **NEVER write requirements the team can't test.** Every requirement must have a clear pass/fail condition. If QA can't verify it, it's not a requirement — it's a wish.

4. **NEVER assume the reader has context.** The PRD should be understandable by someone who joins the project tomorrow. Include background, define terms, link to prior work.

5. **NEVER combine multiple features in one PRD.** If a PRD covers more than one logical feature, split it. Each PRD should be independently implementable and shippable.

6. **ALWAYS include non-goals.** What you're NOT building is as important as what you ARE building. Non-goals prevent scope creep and misaligned expectations.
