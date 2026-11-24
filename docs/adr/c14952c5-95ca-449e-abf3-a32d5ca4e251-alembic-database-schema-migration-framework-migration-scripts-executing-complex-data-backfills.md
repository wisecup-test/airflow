---
version: 2
name: c14952c5-95ca-449e-abf3-a32d5ca4e251-alembic-database-schema-migration-framework-migration-scripts-executing-complex-data-backfills
description: "Alembic Database Schema Migration Framework: Migration Scripts Executing Complex Data Backfills"
metadata:
  actual:
    adr_id: c14952c5-95ca-449e-abf3-a32d5ca4e251
    status: accepted
    candidate_group_id: 32333c4d-2787-4808-a558-8c0ccf9855a6
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/migrations/versions/**
---

# Alembic Database Schema Migration Framework: Migration Scripts Executing Complex Data Backfills

Date: 2025-02-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/migrations/versions/**`

## Context

- The application persistence architecture requires continuous evolution of relational database schemas across multiple supported database engines.
- Schema modifications require deterministic version tracking, declarative table and constraint definitions, and safe execution paths across divergent database dialects.
- Prior approaches relying on raw SQL scripts created maintenance fragmentation when accommodating structural alterations such as table constraint adjustments across different database backends.

## Problem Statement

Managing relational schema alterations across heterogeneous database engines risks structural drift, constraint violations, and migration failures when engine-specific behaviors diverge. A standardized migration framework is required to ensure deterministic, versioned, and reversible schema evolutions with cross-dialect compatibility.

## Decision

- **R-LM-001** SHOULD: Migration scripts executing complex data backfills SHOULD check the offline mode status of the context and omit interactive database operations when generating static SQL artifacts.

## Policy Block

In scope:

- All relational schema definitions, migrations, alterations, constraint operations, and index modifications within the application persistence layer.
- All data transformation and column backfill scripts executed as part of relational database revision lifecycles.

Out of scope:

- Non-relational data stores, key-value caches, and temporary ephemeral stores that do not utilize relational schema versioning.
- Runtime application querying and ORM domain model instantiation performed outside migration execution contexts.

Exceptions:

- **EXC-20-001** Historical migrations where irreversible data transformations or dropped columns make lossless schema rollback fundamentally impossible.
  Approval: Explicit sign-off from persistence architecture maintainers.
  Documentation: Document the irreversibility and data loss rationale directly in the downgrade docstring and migration metadata.

## Rationale

- Alembic provides a robust foundation for defining programmatic, revision-controlled schema changes with native support for transactional and batch alterations.
- Utilizing batch alteration operations isolates dialect quirks across diverse relational storage engines, preventing platform-specific migration failures.
- Dialect branching within migration scripts allows safe data backfilling tailored to syntax constraints of each supported database dialect.

## Consequences

Positive:

- Provides deterministic, versioned, and reversible relational schema evolution across all application lifecycle phases.
- Abstracts schema alterations across heterogeneous database engines through unified batch alteration operations.
- Enables data backfills and constraint migrations to run atomically alongside structural table modifications.

Negative:

- Authors must maintain multi-branch conditional SQL statements for operations where schema or data modification syntax diverges across database engines.
- Schema changes require synchronized updates across migration scripts and application ORM entity definitions to prevent drift.
- Offline migration script generation requires strict discipline to avoid connection-dependent queries during migration assembly.

## Alternatives

- Raw SQL Script Migrations (rejected)
  Rejected because: Lacks cross-dialect abstraction capabilities, automated dependency ordering, and uniform table restructuring facilities across heterogeneous database engines.
  When valid: Single-database architectures with fixed engine targets where cross-dialect abstraction and ORM schema synchronization are unnecessary.
- Automatic ORM Schema Synchronization at Application Startup (rejected)
  Rejected because: Prevents safe, zero-downtime production deployments, lacks deterministic rollback procedures, and cannot execute complex historical data backfills.
  When valid: Ephemeral development environments or disposable prototypes with no requirements for persisting production state across schema changes.

## Risks

- Dialect incompatibility in raw SQL data backfills causing migration failure on specific supported database engines.
  Mitigation: Enforce mandatory dialect-checking branches and automated multi-engine integration test execution for all migrations containing raw SQL statements.
  Owner: Engineering Team
- Dynamic database queries in migration scripts breaking offline static SQL script generation.
  Mitigation: Guard runtime queries with offline mode status checks and output informational notices when dynamic execution is bypassed.
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Wrap column additions, type modifications, drops, and foreign key constraint changes inside batch alteration contexts to ensure cross-engine portability.
- Inspect the active database dialect name from the migration runtime context prior to executing direct data updates or backfill queries.
- Verify that offline migration execution paths refrain from executing dynamic database query binds or connection fetches.

## Continuation Context

Verify commands:

- Discover and run the project migration verification suite to validate sequential execution of all schema upgrades and downgrades.
- Discover and execute the multi-dialect test suite to confirm migration validity across all configured database engine environments.

Accept when:

- All schema migration revisions execute successfully in forward sequence without unhandled exceptions across all supported database dialect environments.
- Migration downgrade procedures cleanly revert structural changes and restore previous schema state without leaving orphaned constraints or invalid foreign keys.
- Offline execution mode successfully generates static SQL scripts without attempting forbidden dynamic database connection lookups.

## Enforcement

- Verified by: Automated continuous integration test suites executing forward and reverse migration cycles across all configured database engines.
- Verified by: Peer code reviews verifying adherence to batch alteration patterns and dialect-specific execution branching.
- Violation handling: Automated pipeline termination and pull request rejection upon migration failure during schema upgrade or rollback testing.
- Violation handling: Blocking merge requests that introduce unmanaged raw DDL statements or omit batch alteration wrappers for table modifications.
- Exception process: Submit an architectural change request documenting why an irreversible schema migration or engine-specific schema exception is strictly necessary.
- Exception process: Obtain explicit approval from the database architecture owner prior to merging irreversible or dialect-restricted migration revisions.
