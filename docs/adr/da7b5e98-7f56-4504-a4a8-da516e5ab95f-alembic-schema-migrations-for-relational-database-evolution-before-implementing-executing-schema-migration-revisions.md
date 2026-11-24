---
version: 2
name: da7b5e98-7f56-4504-a4a8-da516e5ab95f-alembic-schema-migrations-for-relational-database-evolution-before-implementing-executing-schema-migration-revisions
description: "Alembic Schema Migrations for Relational Database Evolution: Before Implementing Executing Schema Migration Revisions"
metadata:
  actual:
    adr_id: da7b5e98-7f56-4504-a4a8-da516e5ab95f
    status: accepted
    candidate_group_id: 526d9cb5-5562-4c94-9a76-776777e4c522
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/migrations/versions/**
---

# Alembic Schema Migrations for Relational Database Evolution: Before Implementing Executing Schema Migration Revisions

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/migrations/versions/**`

## Context

- The application relies on relational database persistence with multiple supported database dialects across operational environments.
- Evolving domain models and metadata persistence structures require structured, repeatable, and reversible DDL mutations across environments.
- Direct DDL execution across divergent relational engines risks table locking, engine-specific syntax failures, and schema drift.
- A unified migration abstraction is required to manage table creation, constraint updates, and data backfills in a unified lifecycle.

## Problem Statement

Evolving relational database structures across multiple heterogeneous database engines without a unified, reversible migration mechanism causes schema drift, incompatible DDL syntax execution, and uncoordinated data corruption across deployment environments.

## Decision

- **R-DM-001** MUST: Before implementing or executing schema migration revisions, the consumer MUST inspect the repository lock artifact to determine the exact resolved dependency version and confirm API compatibility against official documentation.

## Policy Block

In scope:

- All relational database schema definitions, table creations, column modifications, indices, and constraints.
- All data transformations and backfills executed as part of persistent schema version transitions.

Out of scope:

- In-memory ephemeral cache models and external document store collections.
- Runtime application query logic and data manipulation outside schema revision lifecycles.

Exceptions:

- **EXC-29-001** A migration contains an intrinsically irreversible data transform where original data cannot be reconstructed.
  Approval: Data Architecture Working Group
  Documentation: Explicit justification documented within the revision docstring and exception register

## Rationale

- Alembic provides a standardized migration framework tightly integrated with relational metadata models, supporting programmatic revision management.
- Batch alteration operations abstract away divergent engine capabilities, ensuring that constraint drops and column renames succeed uniformly across diverse dialects.
- Embedding both forward upgrade and reverse downgrade procedures within each migration module ensures rollbacks are reproducible and verifiable in automated test suites.

## Consequences

Positive:

- Provides deterministic, versioned, and auditable history of all relational database schema modifications.
- Enables cross-database compatibility across diverse relational engines through unified batch alteration primitives.
- Guarantees reversible schema evolution with explicit downgrade capabilities for rollback scenarios.
- Supports structured multi-dialect data transformation logic embedded directly within schema lifecycle events.

Negative:

- Developers must author and verify both forward and reverse schema mutations alongside dialect-specific data backfill queries.
- Complex constraint updates on strict relational engines require careful sequencing and batch alteration workflows.
- Schema revisions must be coordinated across deployment pipelines to prevent locking and concurrent migration collisions.

## Alternatives

- Manual SQL execution scripts per target database engine (rejected)
  Rejected because: Increases operational overhead, introduces drift between environments, and lacks programmatic tracking of schema revision history.
  When valid: Admissible only for emergency hotfixes on legacy systems lacking schema migration tooling.
- ORM auto-generation of database tables on application initialization (rejected)
  Rejected because: Prevents zero-downtime rollouts, fails to provide deterministic and reversible migration paths, and cannot safely coordinate multi-step data backfills.
  When valid: Valid only in ephemeral single-process developer testing environments.

## Risks

- Subtle SQL dialect syntax differences can cause data backfills or constraint updates to fail on specific database engines.
  Mitigation: Run comprehensive automated migration integration suites against all supported relational database engines in continuous integration.
  Owner: Data Engineering Team
- Long-running table alterations or locks during migrations can degrade availability in high-throughput production environments.
  Mitigation: Mandate non-destructive multi-phase migration patterns with separate add, backfill, and drop phases across consecutive releases.
  Owner: Persistence Architecture Leads

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- When modifying existing tables with column alterations, index adjustments, or constraint replacements, encapsulate operations within batch alteration contexts to ensure execution across dialects with limited native alteration semantics.
- For data backfill operations accompanying schema changes, query the active dialect name from the migration context and execute dialect-specialized SQL constructs when syntax diverges.

## Continuation Context

Verify commands:

- Discover the database migration test runner from the repository configuration and execute the full revision sequence across all configured database engines.
- Inspect the migration revision history using the project verification script to ensure strictly ordered, acyclic revision dependencies.

Accept when:

- All database schema revisions execute successfully in both upgrade and downgrade directions across all supported relational database backends without unhandled dialect errors.
- Target database catalogs accurately reflect all newly declared tables, altered columns, constraints, and indices matching migration definitions.

## Enforcement

- Verified by: Automated database migration test suites in continuous integration pipelines executing upgrades and downgrades on all supported relational backends.
- Verified by: Peer review of schema revision modules by persistence and data infrastructure maintainers.
- Verified by: Pre-deployment validation verifying that the database metadata revision matches the repository state.
- Violation handling: Automated continuous integration checks reject pull requests containing unversioned schema alterations or missing downgrade logic.
- Violation handling: Direct database schema tampering without corresponding migration revisions blocks promotion through deployment gates.
- Violation handling: Incomplete or failing dialect backfill paths require immediate remediation before release approval.
- Exception process: Propose an architectural exception with documented constraints to the data architecture team.
- Exception process: Obtain formal sign-off from database operations leads before deploying any non-standard schema modification.
- Exception process: Track temporary schema deviation in an architecture tracking issue with a committed resolution schedule.
