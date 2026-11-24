---
version: 2
name: d1dc1f41-21e8-41f7-a420-48daf24f4cf6-sqlalchemy-object-relational-mapping-adoption-backend-persistence-components-define-relational-database
description: "SQLAlchemy Object-Relational Mapping Adoption: Backend Persistence Components Define Relational Database"
metadata:
  actual:
    adr_id: d1dc1f41-21e8-41f7-a420-48daf24f4cf6
    status: accepted
    candidate_group_id: d3f7b496-8afc-44f3-8964-0f6bd32d2727
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SQLAlchemy Object-Relational Mapping Adoption: Backend Persistence Components Define Relational Database

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Relational data persistence is required across core models, database migrations, command-line interfaces, and API endpoints.
- Support for multiple relational database engines necessitates an abstraction layer that isolates application code from dialect-specific SQL syntax.
- Domain entities require explicit attribute typing, relational constraints, and predictable transaction lifecycles.
- High-throughput transactional workflows demand standardized statement construction, row locking, and eager-loading optimizations.

## Problem Statement

Direct SQL manipulation and disparate data access strategies across models, administrative commands, and API endpoints introduce risks of schema divergence, dialect incompatibility across database engines, and lack of type safety. A unified object-relational mapping architecture is required to standardize entity definitions, enforce referential integrity, and provide consistent, type-safe statement execution across the entire application persistence layer.

## Decision

- **R-LM-001** MUST: Backend persistence components MUST define relational database models using SQLAlchemy declarative mapping with typed Mapped attributes and mapped_column declarations.

## Policy Block

In scope:

- All domain entities, schema migrations, and persistence layer abstractions interacting with relational databases.
- Command-line operations and API endpoints that query, insert, update, or delete relational database entities.

Out of scope:

- Ephemeral in-memory cache operations that do not persist to relational storage.
- External service integrations communicating exclusively through network transport protocols.

## Rationale

- SQLAlchemy provides declarative mapping that combines explicit schema declarations with type annotations directly within domain entity classes.
- Unified statement execution standardizes query composition and execution patterns across models, commands, and API handlers.
- Built-in relational constraints and eager loading options ensure referential integrity and prevent common query performance degradation.

## Consequences

Positive:

- Declarative entity definitions provide type safety and structural validation across all persistent models.
- Dialect abstraction enables consistent execution across distinct relational database engines without modifying query logic.
- Fine-grained query control through statement constructs and eager loading prevents excessive database queries and memory bloat.
- Centralized schema definition facilitates automated migration management and database constraint enforcement.

Negative:

- Introduces an abstraction layer requiring developers to understand session lifecycles, statement execution, and loading behaviors.
- Advanced database optimizations and non-standard query constructs require specialized ORM expressions rather than arbitrary SQL strings.

## Alternatives

- Raw SQL execution with manual connection handling (rejected)
  Rejected because: Eliminates type-safe schema modeling, increases maintenance overhead across distinct database engines, and increases risk of SQL injection vulnerabilities.
  When valid: Only appropriate in isolated scripts with minimal database interactions where third-party libraries cannot be introduced.
- Active Record pattern binding domain entities directly to database connections (rejected)
  Rejected because: Couples business logic directly to physical database connections and hinders unit testing and complex transaction coordination.
  When valid: Appropriate for simple applications with uniform database access and minimal domain logic.

## Risks

- Unoptimized relationship queries causing excessive database roundtrips during entity serialization.
  Mitigation: Require explicit eager loading options on query constructs spanning entity relationships.
  Owner: Engineering team
- Divergence between declarative model definitions and migration revisions during schema evolution.
  Mitigation: Enforce automated migration checks and schema verification in continuous integration.
  Owner: Engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Inherit all persistent domain models from a central declarative base and declare attributes using typed mapped column definitions.
- Execute database operations through statement constructs on active sessions, utilizing row locking and eager loading where appropriate.
- Define table-level constraints and indices explicitly within table arguments to enforce database-level integrity.

## Continuation Context

Verify commands:

- Discover the project test runner configuration and execute the persistence and integration test suites.
- Discover the repository static type checker configuration and run type analysis across all persistence and data access modules.
- Discover the database migration execution script and validate that schema definitions match migration revisions.

Accept when:

- All persistence models and query operations pass automated integration tests across supported database configurations.
- Static type analysis completes without errors across all declarative model definitions and statement executions.
- Database migration validation confirms complete parity between declarative models and database schema revisions.

## Enforcement

- Verified by: Automated testing pipelines verifying persistence operations and database migrations.
- Verified by: Static analysis tools validating declarative model type annotations and query construct usage.
- Verified by: Peer code reviews evaluating schema changes and query efficiency.
- Violation handling: Pipeline check failures blocking pull requests that introduce raw SQL queries or malformed declarative mappings.
- Violation handling: Mandatory refactoring of non-compliant data access logic before merging into main branches.
- Exception process: Formal review and approval by technical leads for specialized queries that cannot be represented using standard ORM constructs.
- Exception process: Documentation of rationale and isolation of custom database logic behind dedicated interfaces.
