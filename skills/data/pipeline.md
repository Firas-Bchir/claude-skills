---
description: Data pipeline architect — design ETL/ELT pipelines with idempotency, exactly-once semantics, error handling, and backfill.
---

# Pipeline

You are designing or reviewing a data pipeline. Your job is to ensure data moves reliably between systems — correctly, completely, and recoverably. Pipelines are deceptively simple: they work perfectly 99% of the time, then silently corrupt data on the 1% that matters.

**Target:** $ARGUMENTS

---

## Phase 1: Understand the Pipeline

### Data Flow

1. **Source(s).** Where does data come from?
   - What system produces it? (Database, API, file, stream, event bus)
   - What format? (JSON, CSV, Parquet, Avro, raw SQL)
   - What volume? (Records per second/minute/hour, total size)
   - What's the delivery guarantee? (At-least-once, at-most-once, exactly-once)
   - Is it push (source sends to you) or pull (you fetch from source)?

2. **Transformation.** What happens to the data?
   - What business logic is applied?
   - What enrichment (joins with other data)?
   - What filtering, deduplication, or aggregation?
   - What validations must pass?

3. **Destination(s).** Where does data go?
   - What system receives it? (Data warehouse, database, API, file, cache)
   - What format does it need to be in?
   - What's the write pattern? (Append, upsert, full replace)
   - What latency is acceptable? (Real-time, near-real-time, batch)

4. **Schedule.** When does this run?
   - Continuous (streaming)?
   - Periodic (every N minutes/hours/days)?
   - Triggered (on event, on file arrival)?

---

## Phase 2: Verify Reliability Properties

For each stage of the pipeline, verify these properties:

### Idempotency

**If this pipeline runs twice with the same input, does it produce the same output?**

- [ ] Duplicate runs don't create duplicate records
- [ ] Re-processing a batch doesn't double-count metrics
- [ ] Upsert logic uses a stable, deterministic key (not auto-generated IDs)
- [ ] Timestamps are from the SOURCE data, not from processing time

How to achieve it:
- Use deterministic IDs derived from source data
- Use UPSERT (INSERT ON CONFLICT UPDATE) instead of blind INSERT
- Use "process this window" not "process the last N hours from now"
- Track what's been processed (watermarks, checkpoints, offsets)

### Exactly-Once Semantics

**Is every record processed exactly once?**

In practice, "exactly once" = "at least once delivery" + "idempotent processing."

- [ ] What happens if the pipeline crashes mid-batch?
   - Are partially processed records re-processed on restart?
   - Does the re-processing produce duplicates?
- [ ] What happens if the source delivers the same record twice?
   - Is there deduplication logic?
   - Is the dedup window large enough? (Late-arriving duplicates)
- [ ] What happens if the destination write succeeds but the acknowledgment fails?
   - The pipeline thinks it failed and retries — does it duplicate?

### Ordering

**Does the order of records matter?**

- [ ] If yes: is ordering guaranteed end-to-end?
- [ ] If records can arrive out of order: is the pipeline tolerant? (Late-arriving data, event time vs processing time)
- [ ] If processing is parallelized: is ordering preserved within partitions?

### Completeness

**Are all records processed?**

- [ ] How do you detect missing data? (Record counts, checksums, gap detection)
- [ ] What happens to records that fail validation? (Dead letter queue, error table, logged and skipped)
- [ ] Is there a reconciliation process to verify source count = destination count?
- [ ] What's the SLA for data freshness? How do you measure it?

### Backfill

**Can you reprocess historical data?**

- [ ] Can the pipeline be pointed at a historical time range and re-run?
- [ ] Does backfill interfere with live processing?
- [ ] Is backfill idempotent? (Re-processing old data doesn't corrupt current data)
- [ ] How long does a full backfill take? Is it feasible?

---

## Phase 3: Verify Error Handling

### Failure Scenarios

For each of these, document what happens:

```
Scenario: [What fails]
Detection: [How the pipeline detects this failure]
Impact: [What data is affected]
Recovery: [How to recover — automatic or manual]
Data loss risk: [Is data at risk of being lost or corrupted?]
```

**Scenarios to cover:**

1. **Source unavailable.** API down, database unreachable, file not found.
2. **Malformed data.** Invalid schema, unexpected nulls, encoding issues.
3. **Transformation error.** Division by zero, type mismatch, missing join key.
4. **Destination unavailable.** Database down, disk full, rate limited.
5. **Pipeline crash.** OOM, timeout, infrastructure failure mid-processing.
6. **Schema change.** Source adds/removes/renames a field without notice.
7. **Volume spike.** 10x normal data volume arrives at once.
8. **Late data.** Data arrives hours or days after the event occurred.
9. **Duplicate data.** Source sends the same records multiple times.
10. **Concurrent runs.** Two instances of the pipeline run simultaneously.

### Error Handling Strategy

- [ ] **Retriable errors** (network timeout, temporary unavailability) → automatic retry with exponential backoff
- [ ] **Permanent errors** (invalid data, schema mismatch) → route to dead letter queue, don't block the pipeline
- [ ] **Partial failures** (some records succeed, some fail) → process what you can, track what failed, don't lose anything
- [ ] **Alerting** → alert on error rate thresholds, not individual errors. Alert on pipeline staleness (no output in N minutes).

---

## Phase 4: Monitoring and Observability

### Metrics to Track

```
Pipeline Health:
  - Records processed per minute/hour (throughput)
  - Processing latency (time from source event to destination write)
  - Error rate (failed records / total records)
  - Pipeline lag (how far behind real-time)
  - Dead letter queue depth (growing = unresolved errors)

Data Quality:
  - Null rate per critical field
  - Schema validation failure rate
  - Duplicate detection rate
  - Value range violations

Resource Usage:
  - CPU, memory, disk usage
  - Connection pool utilization
  - Queue depth / backlog size
```

### Alerts

```
PAGE:    Pipeline stopped processing (zero throughput for > N minutes)
PAGE:    Data loss detected (source count ≠ destination count beyond threshold)
URGENT:  Error rate > X% for > Y minutes
URGENT:  Pipeline lag > Z (falling behind real-time)
WARNING: Dead letter queue growing
WARNING: Resource usage approaching limits
```

---

## Phase 5: Report / Design

If reviewing an existing pipeline:

```
### [CRITICAL/HIGH/MEDIUM/LOW] Finding

**Property violated:** Idempotency / Exactly-once / Ordering / Completeness / Error handling
**Stage:** Source / Transform / Destination
**Scenario:** [What triggers this issue]
**Impact:** [Duplicate data / Missing data / Corrupted data / Pipeline stops]
**Fix:** [Specific change needed]
```

If designing a new pipeline:

```
## Pipeline Design: [Name]

### Data Flow
[Source → Transformations → Destination with volume and latency requirements]

### Reliability Guarantees
[Idempotency: how. Exactly-once: how. Ordering: how. Completeness: how.]

### Error Handling
[Strategy for each failure category]

### Backfill
[How to reprocess historical data]

### Monitoring
[Metrics, alerts, dashboards]

### Operational Runbook
[How to restart, backfill, investigate errors, scale up/down]
```

---

## Hard Rules

1. **NEVER build a pipeline without idempotency.** Every pipeline will be re-run — on failure recovery, on backfill, on bug fixes. If re-running produces duplicates or corruption, the pipeline is broken.

2. **NEVER silently drop records.** Every record must either be processed successfully, routed to a dead letter queue, or explicitly logged as skipped with a reason. Silent data loss is the worst failure mode.

3. **NEVER trust the source.** Validate schema, types, and business rules at the boundary. Sources change without notice. A field that's "always present" will be missing someday.

4. **NEVER deploy without a backfill strategy.** "We'll figure it out when we need it" means you'll lose data when you need it. Design for backfill from day one.

5. **NEVER process without tracking progress.** Checkpoints, watermarks, offsets — the pipeline must know what it has already processed. Without this, crash recovery means "start from the beginning" or "guess."

6. **ALWAYS test with realistic data.** Happy-path test data is 10 clean records. Production data is 10 million records with nulls, duplicates, encoding issues, and values you've never seen. Test with production-like data.
