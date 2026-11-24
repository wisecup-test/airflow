---
version: 2
name: cross-cutting-before-implementing-modifying-migration-080d
description: "Alembic Database Schema Migration Framework: Before Implementing Modifying Migration Operations Consumer"
metadata:
  actual:
    adr_id: 080d485b-4ca2-43a3-87d4-6ed8d28006d0
    status: accepted
    candidate_group_id: 32333c4d-2787-4808-a558-8c0ccf9855a6
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/migrations/versions/**
---

# Alembic Database Schema Migration Framework: Before Implementing Modifying Migration Operations Consumer

Scope: applies to `airflow-core/src/airflow/migrations/versions/**`.

These rules are ALWAYS ACTIVE for:
- All relational schema definitions, migrations, alterations, constraint operations, and index modifications within the application persistence layer.
- All data transformation and column backfill scripts executed as part of relational database revision lifecycles.

### Rules

- **R-LM-001** MUST: Before implementing or modifying migration operations, the consumer MUST inspect the repository lock artifact to determine the exact resolved version of Alembic and verify that all referenced APIs conform to that specific version documentation.

### Verify

- Discover and run the project migration verification suite to validate sequential execution of all schema upgrades and downgrades.
- Discover and execute the multi-dialect test suite to confirm migration validity across all configured database engine environments.

**Accept when:**

- All schema migration revisions execute successfully in forward sequence without unhandled exceptions across all supported database dialect environments.
- Migration downgrade procedures cleanly revert structural changes and restore previous schema state without leaving orphaned constraints or invalid foreign keys.
- Offline execution mode successfully generates static SQL scripts without attempting forbidden dynamic database connection lookups.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites executing forward and reverse migration cycles across all configured database engines.
- Verified by: Peer code reviews verifying adherence to batch alteration patterns and dialect-specific execution branching.
- Violation handling: Automated pipeline termination and pull request rejection upon migration failure during schema upgrade or rollback testing.
- Violation handling: Blocking merge requests that introduce unmanaged raw DDL statements or omit batch alteration wrappers for table modifications.
- Exception process: Submit an architectural change request documenting why an irreversible schema migration or engine-specific schema exception is strictly necessary.
- Exception process: Obtain explicit approval from the database architecture owner prior to merging irreversible or dialect-restricted migration revisions.
</enforcement>
