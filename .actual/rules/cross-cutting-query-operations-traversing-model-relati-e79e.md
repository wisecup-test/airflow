---
version: 2
name: cross-cutting-query-operations-traversing-model-relati-e79e
description: "SQLAlchemy Object-Relational Mapping Adoption: Query Operations Traversing Model Relationships Specify"
metadata:
  actual:
    adr_id: e79e366a-0fb8-45d5-8ba9-5a743d5058d6
    status: accepted
    candidate_group_id: d3f7b496-8afc-44f3-8964-0f6bd32d2727
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SQLAlchemy Object-Relational Mapping Adoption: Query Operations Traversing Model Relationships Specify

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All domain entities, schema migrations, and persistence layer abstractions interacting with relational databases.
- Command-line operations and API endpoints that query, insert, update, or delete relational database entities.

### Rules

- **R-LM-001** SHOULD: Query operations traversing model relationships SHOULD specify explicit eager loading options such as joinedload, contains_eager, or selectin to prevent redundant database roundtrips.

### Verify

- Discover the project test runner configuration and execute the persistence and integration test suites.
- Discover the repository static type checker configuration and run type analysis across all persistence and data access modules.
- Discover the database migration execution script and validate that schema definitions match migration revisions.

**Accept when:**

- All persistence models and query operations pass automated integration tests across supported database configurations.
- Static type analysis completes without errors across all declarative model definitions and statement executions.
- Database migration validation confirms complete parity between declarative models and database schema revisions.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated testing pipelines verifying persistence operations and database migrations.
- Verified by: Static analysis tools validating declarative model type annotations and query construct usage.
- Verified by: Peer code reviews evaluating schema changes and query efficiency.
- Violation handling: Pipeline check failures blocking pull requests that introduce raw SQL queries or malformed declarative mappings.
- Violation handling: Mandatory refactoring of non-compliant data access logic before merging into main branches.
- Exception process: Formal review and approval by technical leads for specialized queries that cannot be represented using standard ORM constructs.
- Exception process: Documentation of rationale and isolation of custom database logic behind dedicated interfaces.
</enforcement>
