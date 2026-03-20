---
description: Zero-downtime migration — plan data and system migrations with rollback strategy, integrity checks, and no service interruption.
---

# Migrate

You are planning a migration. Something needs to move — data between schemas, services between platforms, code between architectures. Migrations are the most dangerous operations in software: they touch live data, affect all users simultaneously, and are hard to reverse. Your job is to make this migration boring and safe.

**Migration:** $ARGUMENTS

---

## Phase 1: Define the Migration

### What's Moving?

1. **Source state.** What exists today? Schema, format, location, volume.
2. **Target state.** What should exist after the migration? Schema, format, location.
3. **Delta.** What specifically changes? New fields? Changed types? New relationships? Different service?
4. **Volume.** How much data? How many records? How large? This determines whether you can migrate in one shot or need batching.

### Why Now?

- What's the business or technical driver?
- What's the deadline? Is there flexibility?
- What happens if the migration is delayed?
- What happens if the migration fails partway?

### Who's Affected?

- Which services read/write this data?
- Which teams own those services?
- Are there external consumers (third-party integrations, public APIs)?
- What's the user impact during migration?

---

## Phase 2: Choose the Strategy

### Option A: Big Bang Migration

All data migrates at once during a maintenance window.

- **When to use:** Small dataset, simple transformation, acceptable downtime window
- **Risk:** If it fails, everything is down until fixed
- **Rollback:** Restore from backup

### Option B: Dual-Write Migration (Recommended for Most Cases)

Write to both old and new systems simultaneously. Gradually shift reads.

```
Phase 1: Write to OLD. Read from OLD. (current state)
Phase 2: Write to OLD + NEW. Read from OLD. (dual write, verify NEW)
Phase 3: Write to OLD + NEW. Read from NEW. (cutover reads)
Phase 4: Write to NEW only. Read from NEW. (cleanup OLD)
```

- **When to use:** Large dataset, zero downtime required, complex transformation
- **Risk:** Dual-write consistency (OLD and NEW must stay in sync)
- **Rollback:** At any phase, revert to reading/writing OLD only

### Option C: Expand-Contract Migration

Change the schema in backward-compatible stages.

```
Phase 1: EXPAND — Add new columns/fields alongside old ones
Phase 2: MIGRATE — Backfill new fields from old data
Phase 3: TRANSITION — Update code to use new fields
Phase 4: CONTRACT — Remove old columns/fields
```

- **When to use:** Schema changes on existing tables/collections
- **Risk:** Forgetting to handle both old and new format during transition
- **Rollback:** At any phase, code can still read old format

### Option D: Blue-Green Migration

Run old and new systems in parallel. Switch traffic atomically.

- **When to use:** Full service replacement, infrastructure migration
- **Risk:** Data divergence between blue and green during parallel run
- **Rollback:** Switch traffic back to old system

**Present your recommended strategy with reasoning.**

---

## Phase 3: Plan the Migration

### Pre-Migration Checklist

- [ ] **Backup.** Full backup of source data. Verified it can be restored. Tested restore time.
- [ ] **Inventory.** Complete list of all services that read/write this data.
- [ ] **Compatibility.** All services can handle both old AND new format during migration.
- [ ] **Validation.** Script or tool to verify data integrity after migration.
- [ ] **Rollback tested.** You've actually performed the rollback in a test environment.
- [ ] **Monitoring.** Alerts for: migration progress, error rates, data inconsistencies, performance degradation.
- [ ] **Communication.** All affected teams and stakeholders know the plan and timeline.

### Migration Script

Design the migration logic:

```
For each batch of records:
1. Read batch from source
2. Transform to target format
3. Validate transformed data (schema, constraints, referential integrity)
4. Write to target
5. Verify write (read back and compare)
6. Log progress and any failures
7. If failures exceed threshold → HALT and alert
```

### Requirements for the Script

1. **Idempotent.** Running it twice produces the same result. If it fails at record 5000 of 10000, restarting migrates records 5001-10000 without duplicating 1-5000.

2. **Resumable.** Records progress. Can continue from where it stopped.

3. **Batched.** Processes in configurable batch sizes. Not one giant transaction that locks everything.

4. **Throttled.** Rate-limited to avoid overwhelming the database or service. Configurable speed.

5. **Validated.** Every record is validated after transformation and after writing. Invalid records are logged, not silently skipped.

6. **Monitored.** Progress percentage, records/second, error count, estimated time remaining.

7. **Reversible.** Can undo what it did (or the rollback plan doesn't depend on undoing).

---

## Phase 4: Verify

### Before Migration
- [ ] Migration script tested on a copy of production data (not just test data)
- [ ] Rollback script tested on migrated data
- [ ] All dependent services tested against new schema/format
- [ ] Performance tested: migration under load, queries against new schema
- [ ] Monitoring and alerting configured

### During Migration
- [ ] Progress tracking showing records processed / total
- [ ] Error rate below threshold
- [ ] Source system performance not degraded
- [ ] Application error rates normal

### After Migration
- [ ] **Record count matches.** Source count = target count (or documented exclusions)
- [ ] **Data integrity check.** Sample validation of transformed records
- [ ] **Referential integrity.** All foreign keys / references resolve
- [ ] **Application smoke test.** Critical user flows work end-to-end
- [ ] **Performance baseline.** Query times comparable to pre-migration
- [ ] **No orphaned data.** No records left behind that should have migrated
- [ ] **No duplicates.** No records migrated twice

---

## Phase 5: Rollback Plan

For EVERY phase of the migration, document:

```
### Rollback from Phase N

Trigger: [What conditions trigger a rollback — specific metrics, error thresholds]
Steps:
1. [Exact step to revert]
2. [Next step]
Estimated time: [How long the rollback takes]
Data impact: [What happens to data written during the failed phase]
Verification: [How to confirm rollback succeeded]
```

### Critical Question

**Can you roll back after Phase X?**

Some migrations reach a "point of no return" — where old code can't work with new data. Identify this point. Everything before it must be bulletproof. If there IS no point of no return (because both old and new code handle both formats), you're in a much safer position.

---

## Output Format

```
## Migration Plan: [Title]

### Summary
[One paragraph: what's moving, why, and the chosen strategy]

### Strategy: [Big Bang / Dual-Write / Expand-Contract / Blue-Green]
[Why this strategy was chosen over alternatives]

### Phases
[Ordered list of phases with: what happens, what to verify, how to roll back]

### Rollback Plan
[Phase-by-phase rollback procedures]

### Risks
[What could go wrong and how it's mitigated]

### Timeline
[Estimated duration per phase]

### Checklist
[Pre/during/post checks]
```

---

## Hard Rules

1. **NEVER migrate without a backup.** And not just "we have backups" — a tested, verified backup that you've actually restored in a test environment.

2. **NEVER migrate all data at once without testing on a subset first.** Migrate 100 records. Verify. Migrate 10,000. Verify. Then migrate everything.

3. **NEVER assume the rollback works.** Test it. Actually run the rollback in a test environment and verify the system returns to a working state.

4. **NEVER migrate during peak traffic.** Even zero-downtime migrations add load. Do heavy lifting during low-traffic windows.

5. **NEVER skip the compatibility window.** There must be a period where BOTH old and new formats work. Deploying new code and new schema simultaneously is a recipe for outages.

6. **NEVER treat "migration complete" as "cleanup complete."** Remove old code, old tables, old flags, and dual-write logic AFTER the migration is proven stable — not on the same day.

7. **ALWAYS have a clear go/no-go decision point.** Before starting the irreversible phase (if any), explicitly decide: proceed or abort. With data, not gut feeling.
