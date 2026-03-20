---
description: API & schema design — design data models and interfaces that evolve gracefully with versioning, validation, and migration paths.
---

# Design

You are designing a data model, API, or system interface. Good design is invisible — it just works, evolves without breaking, and makes the wrong thing hard to do. Bad design haunts every developer who touches the system for years.

**Target:** $ARGUMENTS

---

## Phase 1: Understand the Domain

Before drawing boxes and arrows, understand the REAL problem.

1. **What entities exist?** What are the real-world things this system models? (Users, Orders, Products — not tables, not classes. Things.)

2. **What are the relationships?** How do entities relate?
   - One-to-one, one-to-many, many-to-many?
   - Required or optional?
   - Does the relationship have its own data? (An "enrollment" between student and course has a grade, a date, a status)

3. **What are the invariants?** Rules that must ALWAYS be true:
   - "An order must have at least one line item"
   - "A user's email must be unique"
   - "A balance can never be negative"
   - These become constraints, validations, and transactions in the design.

4. **What are the access patterns?** How will this data be read and written?
   - What queries are most frequent? (Design for the common case)
   - What queries must be fast? (These drive indexing decisions)
   - What data is read together? (This drives denormalization decisions)
   - What data changes together? (This drives transaction boundaries)

5. **What changes over time?** Which fields are immutable? Which get updated frequently? Which get appended to? This drives architectural choices between mutable records, event sourcing, and append-only logs.

---

## Phase 2: Design the Schema

### Data Model

For each entity, define:

```
Entity: [Name]
Purpose: [Why this entity exists — one sentence]

Fields:
  - [field_name]: [type] [constraints] — [what it represents]
  - [field_name]: [type] [constraints] — [what it represents]

Relationships:
  - [relationship description] → [target entity]

Indexes:
  - [fields] — [what query this supports]

Invariants:
  - [rule that must always be true]

Access patterns:
  - READ: [description] — [frequency]
  - WRITE: [description] — [frequency]
```

### Schema Rules

1. **Name things for the domain, not the implementation.** `order_status` not `status_flag_int`. `customer_email` not `varchar_field_3`.

2. **Every field must justify its existence.** If you can't name the feature or query that needs a field, don't add it. YAGNI applies to schemas.

3. **Choose types that prevent invalid data.**
   - Use enums for fixed sets of values, not strings
   - Use timestamps, not string dates
   - Use integers for money (cents), not floats
   - Use UUIDs for IDs that need to be globally unique
   - Use boolean only for true binary states (not for tri-state "yes/no/unknown")

4. **Make invalid states unrepresentable.** If an order can only have a `shipped_at` when status is "shipped," model it so those can't be out of sync.

5. **Default to NOT NULL.** Every nullable field is a question: "what does null MEAN here?" If null means "not set yet," maybe the field should be set at creation. If null means "not applicable," maybe it belongs on a different entity.

6. **Normalize until it hurts, then denormalize where it matters.** Start normalized (no duplication). Denormalize only when you have measured performance evidence AND a strategy for keeping the copies in sync.

---

## Phase 3: Design the API

### Endpoints / Interface

For each operation the system supports:

```
[METHOD] /path/to/resource
Purpose: [What this does — one sentence]
Auth: [What permission is required]

Request:
  Parameters: [path params, query params]
  Body: [schema with types and constraints]
  Example: [concrete example]

Response:
  Success (200/201): [schema with example]
  Errors:
    400: [when and why — specific validation failures]
    401: [when — not authenticated]
    403: [when — not authorized]
    404: [when — resource not found]
    409: [when — conflict / duplicate]
    422: [when — semantically invalid]

Side effects: [What else changes — notifications sent, caches invalidated, events emitted]
Idempotency: [Can this be safely retried? How?]
```

### API Rules

1. **Be consistent.** If `GET /users/:id` returns a user, `GET /orders/:id` should return an order with the same envelope format, error format, and pagination approach.

2. **Use standard HTTP semantics.**
   - GET = read (safe, idempotent)
   - POST = create (not idempotent unless you design it to be)
   - PUT = full replace (idempotent)
   - PATCH = partial update (idempotent)
   - DELETE = remove (idempotent)

3. **Version from day one.** `/v1/users` not `/users`. Changing a published API without versioning breaks every client.

4. **Paginate all lists.** No endpoint should return unbounded results. Default limit, maximum limit, cursor-based or offset-based pagination.

5. **Return what was created/updated.** After a POST or PUT, return the full resource so the client doesn't need a separate GET.

6. **Use meaningful error responses.** Not just a status code — a body with: error code (machine-readable), message (human-readable), field-level errors (for validation).

7. **Design for the client, not the database.** The API is a product. If the client needs data from 3 tables to render a screen, consider an endpoint that combines them — don't force 3 round trips.

---

## Phase 4: Validate the Design

### Evolution Test

For each likely future change, verify the design can handle it:

1. **Adding a field** — Can you add a field without breaking existing clients?
2. **Removing a field** — Can you deprecate and remove a field gracefully?
3. **Changing a field type** — Can you migrate from string to enum, int to bigint?
4. **Adding a relationship** — Can you add a new entity linked to an existing one?
5. **Splitting an entity** — If an entity grows too large, can it be decomposed?
6. **Changing business rules** — If an invariant changes, how does the schema adapt?

### Performance Test

For each access pattern identified in Phase 1:

1. **What's the query plan?** Does it use an index or scan the whole table/collection?
2. **What happens at 10x scale?** If the table has 10M rows instead of 10K, does the query still work?
3. **What's the write load?** Hot spots (everyone writing to the same row/document)?
4. **What's the read-to-write ratio?** Does the design match?

### Security Test

1. **Can any field leak sensitive data?** (Passwords in responses, PII in logs)
2. **Are authorization boundaries in the data model?** (Tenant IDs, owner IDs — can queries scope correctly?)
3. **Can input validation be enforced by the schema?** (Type constraints, enums, check constraints)

---

## Phase 5: Present the Design

```
## Summary
[One paragraph: what this design does and the key decisions]

## Data Model
[Entity definitions with relationships, rendered as a diagram or structured list]

## API Surface
[Endpoint definitions with request/response schemas]

## Key Decisions
[For each non-obvious choice: what was decided, what alternatives were considered, why]

## Migration Path
[If this changes existing systems: how to get from current state to new design]

## Known Limitations
[What this design does NOT handle, and why that's acceptable for now]
```

**STOP HERE.** Wait for approval before implementation.

---

## Hard Rules

1. **NEVER design without understanding access patterns.** A schema that looks elegant on paper but can't support the queries you need is useless. Know your reads and writes first.

2. **NEVER use generic names.** `data`, `info`, `value`, `type`, `status` — these mean nothing without context. Be specific: `payment_status`, `verification_type`, `account_balance`.

3. **NEVER store derived data without a sync strategy.** If you denormalize (store a computed value), you MUST have a mechanism to keep it in sync. Otherwise it WILL drift and become a lie.

4. **NEVER design a public API that exposes internal structure.** If your API returns database column names, internal IDs, or implementation details, you've coupled your clients to your implementation. You can never change the internals without breaking them.

5. **NEVER skip the migration plan.** Every schema change on a live system needs a plan for: deploying without downtime, handling old and new data simultaneously, and rolling back if something goes wrong.

6. **NEVER design for "later."** Design for what you need NOW, with enough flexibility that "later" doesn't require a rewrite. Adding a field later is easy. Splitting a table later is hard. Know the difference.
