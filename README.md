# Claude Skills

**Expert-level workflows for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Each skill turns Claude into a domain specialist with battle-tested protocols.**

Skills are structured prompts that enforce proven workflows. Instead of hoping Claude does the right thing, skills guarantee it — with mandatory steps, hard rules, and verification gates.

> Built from real production incidents, security audits, and engineering workflows. Not theory — practice.

## How to Use

Skills work as [custom slash commands](https://docs.anthropic.com/en/docs/claude-code/tutorials#create-custom-slash-commands) in Claude Code. Drop a `.md` file into the right directory and it becomes a `/command` you can use in any session.

### Install All Skills (Recommended)

Make every skill available in every Claude Code session:

```bash
# Clone the repo
git clone https://github.com/Firas-Bchir/claude-skills.git

# Create your commands directory (if it doesn't exist)
mkdir -p ~/.claude/commands

# Copy all skills
find claude-skills/skills -name "*.md" ! -name "README.md" -exec cp {} ~/.claude/commands/ \;
```

That's it. Open Claude Code and type `/` — you'll see all 36 skills ready to use.

### Install a Single Skill

If you only want specific skills:

```bash
cp claude-skills/skills/engineering/build.md ~/.claude/commands/build.md
```

### Install for a Specific Project

Share skills with your team by adding them to your project's repo:

```bash
# Inside your project directory
mkdir -p .claude/commands
cp claude-skills/skills/engineering/build.md .claude/commands/build.md
```

Project-level skills are shared via git — everyone who clones the repo gets them.

### User-Level vs Project-Level

| Location | Scope | Shared with Team |
|----------|-------|-----------------|
| `~/.claude/commands/` | Available in **all** your projects | No — only on your machine |
| `your-project/.claude/commands/` | Available in **that project** only | Yes — committed to git |

### Using a Skill

Type the skill name followed by what you want:

```
/build add a user authentication flow with OAuth2
/fix the login button doesn't respond on mobile
/hack check the payment flow for vulnerabilities
/review
```

Everything after the command name becomes the skill's input, so be specific about what you need.

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

## Staying Updated

Pull the latest skills anytime:

```bash
cd claude-skills && git pull

# Re-copy to your commands directory
find skills -name "*.md" ! -name "README.md" -exec cp {} ~/.claude/commands/ \;
```

## What Makes a Great Skill

1. **Opinionated** — Hard rules, not suggestions. "NEVER do X" beats "consider avoiding X."
2. **Sequential** — Mandatory steps in order. No skipping to the "fun part."
3. **Verification gates** — Built-in checkpoints that catch mistakes before they ship.
4. **Battle-tested** — Born from real incidents, not hypothetical best practices.
5. **Scope-limited** — Does one thing extremely well. No Swiss Army knives.

## Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for the skill quality standards and submission process.

**The bar is high.** Every skill in this repo should feel like it was written by the best practitioner in that domain. Generic advice doesn't make the cut.

## License

MIT License — use these skills however you want.

---

**Built by [Firas Bchir](https://github.com/firasbchir)**
