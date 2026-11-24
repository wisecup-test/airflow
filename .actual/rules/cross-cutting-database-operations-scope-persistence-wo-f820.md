---
version: 2
name: cross-cutting-database-operations-scope-persistence-wo-f820
description: "Adoption of SQLAlchemy ORM for Domain Modeling and Data Persistence: Database Operations Scope Persistence Work Within"
metadata:
  actual:
    adr_id: f820927b-534d-4b6d-966e-dc18bd94f866
    status: accepted
    candidate_group_id: 7b52cffe-2b32-4abf-8983-3fb04b7fe068
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of SQLAlchemy ORM for Domain Modeling and Data Persistence: Database Operations Scope Persistence Work Within

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Relational database schema modeling, entity relationship definitions, and persistence operations.
- API route handlers, service components, and background tasks executing queries against the relational store.

### Rules

- **R-LM-001** SHOULD: Database operations SHOULD scope persistence work within managed session contexts and commit transactions only at transaction boundaries.

### Verify

- Discover and run the project static analysis and type check validation scripts to confirm ORM mapping compliance.
- Discover and execute the persistence test suite to verify database schema creation, relationship loading, and transaction rollbacks.
- Discover and run the database migration validation scripts to verify schema constraints against target engines.

**Accept when:**

- All entity mappings pass static type checking and declarative model validation without schema errors.
- Relational queries demonstrate deterministic query execution counts and explicit eager loading configurations in integration tests.
- All persistence transactions complete cleanly without orphaned database connections or unresolved session states.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks executing repository test suites and static analysis linters.
- Verified by: Mandatory peer code reviews validating that new persistence logic adheres to ORM mapping and query loading conventions.
- Violation handling: Pull requests introducing unapproved raw SQL queries or unconfigured relationship traversals are blocked by automated review gates.
- Violation handling: Violations identified in post-merge audits must be remediated in a dedicated refactoring change.
- Exception process: Exceptions for specialized performance-critical queries must be formally proposed with benchmark evidence and approved by technical architecture leads.
</enforcement>
