---
description: Database schema review — normalization, indexing strategy, constraint validation, migration safety, and evolution planning.
---

# Schema

You are reviewing a database schema. Your job is to verify that the data model is correct, performant, safe, and evolvable. A bad schema is technical debt that compounds with every row and every query — it gets harder to fix the longer you wait.

**Target:** $ARGUMENTS

---

## Phase 1: Understand the Schema

1. **Read the full schema.** Every table, collection, column, index, constraint, and relationship. Not just the part the user is asking about — the full data model.

2. **Map the entities and relationships.** Draw the entity-relationship model:
   - What are the core entities?
   - How do they relate? (one-to-one, one-to-many, many-to-many)
   - What are the junction/bridge tables?
   - What are the reference/lookup tables?

3. **Understand the access patterns.** How is this data read and written?
   - What are the most frequent queries?
   - What are the most critical queries (user-facing, revenue-impacting)?
   - What's the read-to-write ratio?
   - What are the data volumes? (rows per table, growth rate)

---

## Phase 2: Review

### Normalization

- [ ] **No redundant data.** Is the same fact stored in multiple places? If an address changes, does it need to be updated in multiple tables? Redundancy = inconsistency risk.

- [ ] **Appropriate normalization level.** Most schemas should be in 3NF (Third Normal Form):
  - 1NF: No repeating groups, atomic values
  - 2NF: No partial dependencies (every non-key column depends on the FULL primary key)
  - 3NF: No transitive dependencies (non-key columns depend on the key, not on other non-key columns)

- [ ] **Intentional denormalization.** If data is denormalized (e.g., stored name on order instead of just user_id), is it intentional and documented? Is there a mechanism to keep it in sync?

- [ ] **No encoding multiple values in one field.** Comma-separated lists, JSON blobs in relational columns, bitfields encoding multiple booleans — these resist querying, indexing, and constraints.

### Data Types

- [ ] **Correct types for the data.** Each column uses the most appropriate type:
  - Monetary values: integer (cents) or decimal — NEVER float
  - Timestamps: timestamp with time zone — NEVER varchar
  - UUIDs: native UUID type if available — not varchar(36)
  - Booleans: boolean type — not int, not char(1)
  - Enums: enum type or lookup table — not unconstrained varchar
  - Email/URL: varchar with validation — not text with no limit

- [ ] **Appropriate size limits.** varchar(255) as a default is lazy. What's the actual max? Name fields, description fields, address fields — each has a real maximum.

- [ ] **Consistent types across tables.** If `user_id` is UUID in one table and INTEGER in another, joins will be painful or impossible.

### Constraints

- [ ] **Primary keys.** Every table has a primary key. Natural keys vs surrogate keys decision is intentional and consistent.

- [ ] **Foreign keys.** Every relationship is enforced by a foreign key constraint. No "soft" references that rely on application code to maintain integrity.

- [ ] **NOT NULL.** Every column that should always have a value has NOT NULL. Ask: "What does NULL mean for this column?" If you can't answer clearly, it should be NOT NULL.

- [ ] **Unique constraints.** Business rules requiring uniqueness (email, username, external ID) are enforced at the database level, not just application level.

- [ ] **Check constraints.** Value range rules are enforced: positive amounts, valid status values, date ranges.

- [ ] **Cascading rules.** ON DELETE and ON UPDATE behaviors are explicitly set and correct:
  - CASCADE: child records deleted/updated automatically
  - SET NULL: reference set to null
  - RESTRICT: prevent delete if children exist
  - The default (RESTRICT or NO ACTION) may not be what you want.

### Indexes

- [ ] **Every foreign key is indexed.** Unindexed foreign keys cause full table scans on joins and cascading deletes.

- [ ] **Frequent query patterns have indexes.** Columns in WHERE, JOIN, ORDER BY, and GROUP BY clauses of frequent queries need indexes.

- [ ] **Composite indexes are in the right order.** Index on `(a, b)` supports queries on `a` and `a + b`, but NOT on `b` alone. Column order matters.

- [ ] **No unnecessary indexes.** Each index slows writes and uses storage. Remove indexes that aren't used by any query.

- [ ] **Covering indexes where beneficial.** For high-frequency read queries, an index that includes all needed columns avoids table lookups entirely.

- [ ] **Partial indexes where appropriate.** If only a subset of rows is queried (e.g., `WHERE active = true`), a partial index on that subset is smaller and faster.

### Naming

- [ ] **Consistent naming convention.** Snake_case or camelCase, singular or plural — pick one and use it everywhere.

- [ ] **Descriptive names.** `user_email` not `email`. `created_at` not `timestamp`. `order_status` not `status`.

- [ ] **No reserved words.** Table or column names that clash with SQL keywords (`order`, `user`, `group`, `table`) cause quoting headaches.

- [ ] **Consistent relationship naming.** Foreign keys follow a pattern: `user_id`, `order_id` — not sometimes `userId`, sometimes `fk_user`, sometimes `user`.

---

## Phase 3: Evolution and Migration Safety

### Schema Evolution

- [ ] **Can columns be added safely?** Adding a NOT NULL column without a default locks the table and backfills all rows. Use nullable + backfill + set NOT NULL, or add with a default.

- [ ] **Can columns be removed safely?** Is the column still referenced in application code, views, or stored procedures? Remove all references FIRST, then drop the column.

- [ ] **Can tables be renamed safely?** Views and stored procedures break. Application queries break. Rename is almost never the right approach — create new, migrate, drop old.

- [ ] **Are migrations reversible?** Every migration should have a corresponding rollback migration. Destructive migrations (DROP TABLE, DROP COLUMN) need extra caution.

### Future-Proofing

- [ ] **Can this schema handle 10x data?** Will the current indexes, partitioning, and query patterns work with 10x the rows?

- [ ] **Is the schema extensible?** Can new features be added without restructuring existing tables? (Adding columns is easy; splitting tables is hard.)

- [ ] **Are there obvious missing columns?** Audit columns (`created_at`, `updated_at`, `created_by`), soft delete (`deleted_at`), versioning — are these present where needed?

---

## Phase 4: Report

For each issue:

```
### [CRITICAL/HIGH/MEDIUM/LOW] Issue Title

**Category:** Normalization / Data Types / Constraints / Indexes / Naming / Migration
**Location:** table.column or table→table relationship

**The issue:**
[What's wrong and why it matters]

**Risk:**
[What happens if this isn't fixed — data corruption, slow queries, migration headaches]

**Fix:**
[Specific ALTER TABLE, CREATE INDEX, or schema change]

**Migration notes:**
[How to apply this fix on a live database — locking implications, backfill needed, rollback plan]
```

### Summary

```
INTEGRITY: [PASS/FAIL — are constraints properly enforced?]
PERFORMANCE: [PASS/FAIL — are indexes sufficient for query patterns?]
EVOLUTION: [PASS/FAIL — can this schema evolve safely?]
CONSISTENCY: [PASS/FAIL — naming, types, patterns consistent?]
```

---

## Hard Rules

1. **NEVER skip constraints because "the application validates."** Applications have bugs. Deployments have race conditions. Database constraints are the last line of defense. They must be there.

2. **NEVER use FLOAT for money.** Floating point arithmetic produces rounding errors. Use INTEGER (cents) or DECIMAL. This is non-negotiable.

3. **NEVER add an index without understanding the write cost.** Every index slows every INSERT, UPDATE, and DELETE on that table. Index the queries you have, not the queries you imagine.

4. **NEVER make destructive schema changes without a rollback plan.** DROP TABLE, DROP COLUMN, ALTER TYPE — these are irreversible. Back up first. Test the migration on a copy of production data.

5. **NEVER store comma-separated values in a column.** If a field contains a list, you need a junction table. CSV in a column can't be indexed, can't have constraints, and can't be joined efficiently.

6. **ALWAYS check the migration on production-scale data.** A migration that takes 100ms on your dev database might take 4 hours and lock the table on production. Test with realistic data volumes.
