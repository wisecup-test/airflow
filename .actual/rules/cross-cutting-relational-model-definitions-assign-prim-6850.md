---
version: 2
name: cross-cutting-relational-model-definitions-assign-prim-6850
description: "Adoption of uuid6 for UUIDv7 Time-Ordered Surrogate Primary Keys: Relational Model Definitions Assign Primary Key"
metadata:
  actual:
    adr_id: 6850af09-5bc6-4166-85e2-b213fe32eced
    status: accepted
    candidate_group_id: d4c4ea06-839a-45ad-b422-6bb9a0d0554e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of uuid6 for UUIDv7 Time-Ordered Surrogate Primary Keys: Relational Model Definitions Assign Primary Key

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All relational database entity models requiring surrogate primary keys.
- Schema migration scripts adding UUID columns or performing backfills on identifier fields.

### Rules

- **R-LM-001** MUST: Relational model definitions MUST assign the primary key column to the standard platform UUID type with default generation delegated to uuid6.uuid7.

### Verify

- Discover and execute the project test runner across model and persistence test suites to verify surrogate key generation.
- Discover and run the project code linter and type checker to confirm compliance with uuid6 model default conventions.

**Accept when:**

- All relational entity models declaring UUID surrogate primary keys specify uuid6.uuid7 as the default generator.
- Database migration scripts executing backfills populate surrogate keys using time-ordered UUID generation consistent with uuid6.uuid7.
- Automated test suites pass without schema validation errors or primary key generation failures.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Static analysis rules inspecting model primary key definitions for uuid6.uuid7 default assignments.
- Verified by: Automated code review and migration script checks ensuring backfills avoid non-sequential identifier generation.
- Verified by: Continuous integration test pipelines executing entity persistence cycles.
- Violation handling: Pull requests introducing non-compliant primary key generators or random UUID defaults are blocked by automated linters.
- Violation handling: Schema migrations using non-standard identifier backfill routines are rejected during review.
- Exception process: Exceptions must be reviewed by the architecture team with documented performance impact analysis on database index locality.
- Exception process: Temporary legacy exceptions must define a remediation plan to migrate to time-ordered UUID generation.
</enforcement>
