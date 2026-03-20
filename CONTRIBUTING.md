# Contributing to Claude Skills

Thanks for wanting to contribute! The goal is simple: **every skill in this repo should be the best version of itself that exists anywhere.**

## The Quality Bar

Before submitting a skill, ask yourself:

1. **Would a senior practitioner approve this workflow?** If a staff engineer, principal security researcher, or senior SRE read your skill, would they nod and say "yes, this is how it should be done"?

2. **Does it prevent real mistakes?** Every hard rule in your skill should trace back to a real incident, bug, or production failure. If you can't explain WHY a rule exists, remove it.

3. **Is it opinionated?** "Consider using transactions" is useless. "NEVER write two Firestore documents without a batch — this is the #1 source of data corruption" is a skill.

## Skill File Format

Each skill is a Markdown file in the `skills/` directory. The filename becomes the command name (e.g., `build.md` → `/build`).

### Structure

```markdown
---
description: One-line description shown in skill selection menus
---

# Skill Title

Brief description of what this skill does, when to use it, and what makes it different from doing the task manually.

[The actual skill prompt — the instructions Claude follows]
```

### Rules for Writing Skills

1. **Use `$ARGUMENTS`** — Always reference `$ARGUMENTS` so users can pass context to the skill.

2. **Enforce ordering** — Use numbered steps with explicit gates: "DO NOT proceed to Step 3 until Step 2 is approved."

3. **Include hard rules** — A dedicated section of non-negotiable rules. These are the guardrails that prevent disasters.

4. **Specify output format** — Tell Claude exactly how to present findings, plans, or results.

5. **Add verification steps** — Every skill should end with self-verification. "Before presenting as done, check X, Y, Z."

6. **Keep it focused** — One skill, one job. If your skill does two things, split it into two skills.

7. **No fluff** — Every sentence should either instruct Claude what to do, prevent a specific mistake, or define an output format. Remove everything else.

## Submission Process

1. Fork the repo
2. Create your skill file in `skills/`
3. Test it thoroughly in Claude Code on real tasks
4. Submit a PR with:
   - The skill file
   - A brief description of what problem it solves
   - An example of the skill in action (input → output)

## Naming Conventions

- **Lowercase, single word** when possible: `build`, `hack`, `fix`
- **Hyphenated** if two words are unavoidable: `threat-model`
- **Verb-first** preferred: describes what the skill DOES
- **Short** — if you need more than 2 words, rethink the name

## What We Don't Accept

- Generic advice repackaged as a skill
- Skills that duplicate existing ones without meaningful improvement
- Framework-specific skills that only work with one technology (skills should be universal)
- Skills without hard rules or verification steps
