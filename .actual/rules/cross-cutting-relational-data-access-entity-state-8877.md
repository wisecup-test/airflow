---
version: 2
name: cross-cutting-relational-data-access-entity-state-8877
description: "SQLAlchemy Unified Data Access and Session Management Architecture: Relational Data Access Entity State Persistence"
metadata:
  actual:
    adr_id: 8877d7f9-4b95-45bd-b14f-b90a7f3515bf
    status: accepted
    candidate_group_id: 745cab4a-008b-4cc1-b804-ea11f419cbb4
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SQLAlchemy Unified Data Access and Session Management Architecture: Relational Data Access Entity State Persistence

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All backend services, REST API endpoints, background execution daemons, and database schema migrations interacting with the primary relational database.

### Rules

- **R-DAP-001** MUST: All relational data access, entity state persistence, and transactional mutations MUST execute through SQLAlchemy Session instances and typed statement constructs including select, update, and delete expressions.

### Verify

- Discover and run the project static analysis suite to verify that database access code adheres to SQLAlchemy statement conventions.
- Discover and execute the project integration test suite across all configured relational database engines to confirm query compatibility and transaction boundary integrity.

**Accept when:**

- All relational queries compile and execute successfully across all supported database dialect environments.
- Static checks pass with zero unmanaged session leaks, raw driver queries, or unhandled dialect branches.
- Integration test suites validate transaction isolation and row locking behavior under concurrent execution.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test matrix running data access suites against all supported database engines.
- Verified by: Peer code review mandatory for changes introducing or modifying database queries, session handling, or migration scripts.
- Violation handling: Pull requests containing untracked raw SQL statements, missing transaction boundaries, or untested dialect branches are blocked from merging.
- Exception process: Exceptions must be submitted via an architectural review issue documenting the technical justification and cross-database impact analysis, requiring approval from the database architecture team.
</enforcement>
