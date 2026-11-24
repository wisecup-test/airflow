---
version: 2
name: cross-cutting-query-operations-expected-return-large-1226
description: "SQLAlchemy Unified Data Access and Session Management Architecture: Query Operations Expected Return Large Result"
metadata:
  actual:
    adr_id: 12266c01-bbd9-4a0a-bc89-d830a18fcabf
    status: accepted
    candidate_group_id: 745cab4a-008b-4cc1-b804-ea11f419cbb4
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SQLAlchemy Unified Data Access and Session Management Architecture: Query Operations Expected Return Large Result

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All backend services, REST API endpoints, background execution daemons, and database schema migrations interacting with the primary relational database.

### Rules

- **R-DAP-001** SHOULD: Query operations expected to return large result sets SHOULD configure batch execution options such as yield_per to stream row tuples and constrain memory consumption.

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
