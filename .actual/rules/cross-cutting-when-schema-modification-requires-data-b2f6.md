---
version: 2
name: cross-cutting-when-schema-modification-requires-data-b2f6
description: "Alembic Schema Migrations for Relational Database Evolution: When Schema Modification Requires Data Transformation"
metadata:
  actual:
    adr_id: b2f6975b-25ce-4c50-8ebe-2c33679275d8
    status: accepted
    candidate_group_id: 526d9cb5-5562-4c94-9a76-776777e4c522
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/migrations/versions/**
---

# Alembic Schema Migrations for Relational Database Evolution: When Schema Modification Requires Data Transformation

Scope: applies to `airflow-core/src/airflow/migrations/versions/**`.

These rules are ALWAYS ACTIVE for:
- All relational database schema definitions, table creations, column modifications, indices, and constraints.
- All data transformations and backfills executed as part of persistent schema version transitions.

### Rules

- **R-DM-001** MUST: When a schema modification requires data transformation or backfilling, the migration revision MUST inspect the active database dialect name and provide dialect-specific SQL execution paths when syntax or join behavior diverges across engines.

### Verify

- Discover the database migration test runner from the repository configuration and execute the full revision sequence across all configured database engines.
- Inspect the migration revision history using the project verification script to ensure strictly ordered, acyclic revision dependencies.

**Accept when:**

- All database schema revisions execute successfully in both upgrade and downgrade directions across all supported relational database backends without unhandled dialect errors.
- Target database catalogs accurately reflect all newly declared tables, altered columns, constraints, and indices matching migration definitions.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated database migration test suites in continuous integration pipelines executing upgrades and downgrades on all supported relational backends.
- Verified by: Peer review of schema revision modules by persistence and data infrastructure maintainers.
- Verified by: Pre-deployment validation verifying that the database metadata revision matches the repository state.
- Violation handling: Automated continuous integration checks reject pull requests containing unversioned schema alterations or missing downgrade logic.
- Violation handling: Direct database schema tampering without corresponding migration revisions blocks promotion through deployment gates.
- Violation handling: Incomplete or failing dialect backfill paths require immediate remediation before release approval.
- Exception process: Propose an architectural exception with documented constraints to the data architecture team.
- Exception process: Obtain formal sign-off from database operations leads before deploying any non-standard schema modification.
- Exception process: Track temporary schema deviation in an architecture tracking issue with a committed resolution schedule.
</enforcement>
