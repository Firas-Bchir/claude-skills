# Claude Skills

**Expert-level workflows for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Each skill turns Claude into a domain specialist with battle-tested protocols.**

Skills are structured prompts that enforce proven workflows. Instead of hoping Claude does the right thing, skills guarantee it — with mandatory steps, hard rules, and verification gates.

> Built from real production incidents, security audits, and engineering workflows. Not theory — practice.

## Quick Start

**1. Pick a skill** from the catalog below.

**2. Copy it** to your project or user commands directory:

```bash
# Project-level (shared with team via git)
cp skills/engineering/build.md your-project/.claude/commands/build.md

# User-level (available in all your projects)
cp skills/engineering/build.md ~/.claude/commands/build.md
```

**3. Use it** in Claude Code:

```
/build add a user authentication flow with OAuth2
```

**Or install all skills at once:**

```bash
# User-level (available in all your projects)
find skills -name "*.md" ! -name "README.md" -exec cp {} ~/.claude/commands/ \;

# Project-level (shared with team via git)
find skills -name "*.md" ! -name "README.md" -exec cp {} your-project/.claude/commands/ \;
```

## The Skills

### [Engineering](skills/engineering/)

| Skill | Short Description | What It Does |
|-------|-------------------|--------------|
| [`/build`](skills/engineering/build.md) | Plan-first implementation | Enforces understand → plan → approve → implement → verify. Eliminates cowboy coding. |
| [`/fix`](skills/engineering/fix.md) | Root cause bug fixing | Traces bugs to their origin, fixes ALL instances of the pattern, not just the reported one. |
| [`/review`](skills/engineering/review.md) | Hostile code review | Reviews uncommitted changes like a senior engineer who's been burned by production bugs. |
| [`/refactor`](skills/engineering/refactor.md) | Safe code restructuring | Restructures code with zero behavior change, proven step-by-step with tests. |
| [`/test`](skills/engineering/test.md) | Test architect | Generates meaningful tests with edge cases, boundary conditions, and mutation-resistant assertions. |
| [`/debug`](skills/engineering/debug.md) | Systematic debugging | Binary search isolation, hypothesis-driven investigation. Never printf-and-pray again. |

### [Security](skills/security/)

| Skill | Short Description | What It Does |
|-------|-------------------|--------------|
| [`/hack`](skills/security/hack.md) | Ethical security audit | Thinks like an attacker — finds injection, auth bypass, data leaks, then shows you how to fix them. |
| [`/armor`](skills/security/armor.md) | Feature hardening | Bulletproofs existing code against edge cases, failures, abuse, and the unexpected. |
| [`/secrets`](skills/security/secrets.md) | Secrets scanner | Finds every hardcoded key, token, password, and connection string in the codebase. |
| [`/lockdown`](skills/security/lockdown.md) | Auth & permissions audit | Traces every access path, finds privilege escalation, verifies server-side enforcement. |

### [Architecture & Planning](skills/architecture/)

| Skill | Short Description | What It Does |
|-------|-------------------|--------------|
| [`/plan`](skills/architecture/plan.md) | System architect | Breaks any task into phases with dependencies, risks, decision points, and rollback strategies. |
| [`/design`](skills/architecture/design.md) | API & schema design | Designs APIs and data models with versioning, backwards compatibility, and migration paths. |
| [`/migrate`](skills/architecture/migrate.md) | Zero-downtime migration | Plans data and system migrations with rollback strategy, integrity checks, and feature flags. |
| [`/scale`](skills/architecture/scale.md) | Performance architect | Identifies bottlenecks and designs solutions for 10x/100x growth with concrete numbers. |

### [Code Quality](skills/quality/)

| Skill | Short Description | What It Does |
|-------|-------------------|--------------|
| [`/clean`](skills/quality/clean.md) | Code quality sweep | Finds dead code, duplication, complexity hotspots, naming issues, and architectural smells. |
| [`/perf`](skills/quality/perf.md) | Performance detective | Profiles, measures, and optimizes with before/after proof. No guessing, only data. |
| [`/a11y`](skills/quality/a11y.md) | Accessibility enforcer | WCAG compliance audit — screen readers, keyboard navigation, color contrast, ARIA labels. |
| [`/i18n`](skills/quality/i18n.md) | Internationalization audit | Finds RTL issues, hardcoded strings, date/number formatting, pluralization bugs. |

### [DevOps & Reliability](skills/devops/)

| Skill | Short Description | What It Does |
|-------|-------------------|--------------|
| [`/ship`](skills/devops/ship.md) | Release checklist | Pre-deployment verification — tests, migrations, rollback plan, monitoring, go/no-go gate. |
| [`/incident`](skills/devops/incident.md) | Incident response | Production fire protocol — triage, mitigate, communicate, root cause, prevent recurrence. |
| [`/monitor`](skills/devops/monitor.md) | Observability architect | Designs what to measure, alert on, and dashboard for any system. |
| [`/docker`](skills/devops/docker.md) | Container review | Dockerfile security, layer optimization, multi-stage builds, supply chain hardening. |

### [Documentation](skills/documentation/)

| Skill | Short Description | What It Does |
|-------|-------------------|--------------|
| [`/explain`](skills/documentation/explain.md) | Code explainer | Traces any flow end-to-end, calibrated to the reader's level. Perfect for onboarding or handoff. |
| [`/adr`](skills/documentation/adr.md) | Architecture Decision Record | Captures the WHY behind decisions — context, options, tradeoffs, consequences. |
| [`/rfc`](skills/documentation/rfc.md) | Technical proposal | Structured proposal — problem, options, tradeoffs, recommendation, rollout plan. |
| [`/postmortem`](skills/documentation/postmortem.md) | Incident postmortem | Blameless incident report — timeline, impact, root cause, action items, follow-up. |

### [Data & Backend](skills/data/)

| Skill | Short Description | What It Does |
|-------|-------------------|--------------|
| [`/sql`](skills/data/sql.md) | SQL surgeon | Query review for correctness, performance, injection prevention, and explain plan analysis. |
| [`/schema`](skills/data/schema.md) | Database schema review | Normalization, indexing strategy, constraint validation, migration safety. |
| [`/pipeline`](skills/data/pipeline.md) | Data pipeline architect | ETL/ELT design with idempotency, exactly-once semantics, error handling, and backfill. |

### [Product & Strategy](skills/product/)

| Skill | Short Description | What It Does |
|-------|-------------------|--------------|
| [`/prd`](skills/product/prd.md) | Product spec writer | From rough idea to detailed requirements with user stories, edge cases, and acceptance criteria. |
| [`/pitch`](skills/product/pitch.md) | Pitch deck architect | Problem, solution, market, traction, business model, ask — structured for impact. |
| [`/estimate`](skills/product/estimate.md) | Estimation framework | Scope breakdown with risk buffers, confidence intervals, and dependency mapping. |
| [`/prioritize`](skills/product/prioritize.md) | Feature prioritization | Impact vs effort matrix with data-backed reasoning and stakeholder alignment. |

### [Learning & Growth](skills/learning/)

| Skill | Short Description | What It Does |
|-------|-------------------|--------------|
| [`/learn`](skills/learning/learn.md) | Code tutor | Explains any concept calibrated to your level, with analogies, examples, and exercises. |
| [`/interview`](skills/learning/interview.md) | Interview architect | Designs technical interview questions, evaluates approaches, provides structured feedback. |
| [`/convert`](skills/learning/convert.md) | Code converter | Ports code between languages/frameworks preserving logic, idioms, and edge case handling. |

## What Makes a Great Skill

Great skills share these properties:

1. **Opinionated** — Hard rules, not suggestions. "NEVER do X" beats "consider avoiding X."
2. **Sequential** — Mandatory steps in order. No skipping to the "fun part."
3. **Verification gates** — Built-in checkpoints that catch mistakes before they ship.
4. **Battle-tested** — Born from real incidents, not hypothetical best practices.
5. **Scope-limited** — Does one thing extremely well. No Swiss Army knives.

## Passing Arguments

Skills support arguments via `$ARGUMENTS`. When you type:

```
/hack check the authentication flow for session fixation
```

The text after the skill name becomes `$ARGUMENTS` in the prompt, letting the skill focus on exactly what you need.

## Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for the skill quality standards and submission process.

**The bar is high.** Every skill in this repo should feel like it was written by the best practitioner in that domain. Generic advice doesn't make the cut.

## License

MIT License — use these skills however you want.

---

**Built by [Firas Bchir](https://github.com/firasbchir)**
