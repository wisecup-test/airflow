---
version: 2
name: 2922c1bd-e152-41a1-a8d9-03accd708b8e-sqlalchemy-declarative-2-0-mapping-with-mapped-and-mapped-column-column-definitions-requiring-database-specific-data
description: "SQLAlchemy Declarative 2.0 Mapping with Mapped and mapped_column: Column Definitions Requiring Database Specific Data"
metadata:
  actual:
    adr_id: 2922c1bd-e152-41a1-a8d9-03accd708b8e
    status: accepted
    candidate_group_id: b1c541c8-c007-4d99-9240-e55b8d2231a7
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SQLAlchemy Declarative 2.0 Mapping with Mapped and mapped_column: Column Definitions Requiring Database Specific Data

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- The persistence layer requires consistent schema definitions and strict referential integrity across relational database backends.
- Multiple core components interact with persistent metadata, necessitating static type verification at development time to prevent runtime schema errors.
- Relational models must handle cross-dialect column variances uniformly without duplicating domain entity classes.

## Problem Statement

Inconsistent relational data models and untyped column definitions lead to schema drift across database dialects, silent runtime attribute errors, and broken referential integrity during entity deletion or state updates.

## Decision

- **R-DM-001** MUST: Column definitions requiring database-specific data types or column lengths MUST use dialect variants through with_variant to preserve portability across supported relational database engines.

## Policy Block

In scope:

- All relational entity definitions representing state, metadata, and operational history in the metadata store.
- All cross-entity relational definitions and table constraints managed by the persistence framework.

Out of scope:

- In-memory cache stores and non-relational ephemeral buffers.
- Client-side transport serializations that do not map directly to relational tables.

## Rationale

- Evidence across thirty-two model modules demonstrates widespread adoption of typed declarative mapping extending a shared base class.
- Using Mapped attributes with mapped_column declarations guarantees compile-time type verification while providing the ORM with explicit column metadata.
- Standardizing foreign key constraints and cascade definitions within model declarations prevents referential orphaned records across dependent tables.

## Consequences

Positive:

- Provides static type safety and IDE autocompletion across all entity model attributes and relationship traversals.
- Standardizes schema constraint definitions, indexing strategies, and lifecycle cascades across the persistence tier.
- Enables automated schema synchronization and cross-database dialect portability through structured variant definitions.

Negative:

- Model declarations require more verbose typing syntax and explicit dialect variant handling for cross-database support.
- Developers must maintain strict synchronization between database migrations and declarative model type annotations.
- Relationship cascades and lazy loading behaviors must be configured deliberately to avoid unoptimized query patterns.

## Alternatives

- Untyped declarative column declarations without type annotations (rejected)
  Rejected because: Untyped column definitions prevent static type checkers from validating attribute access across service boundaries and lead to runtime attribute errors.
  When valid: Valid only in legacy codebases unable to support typed ORM annotations.
- Imperative table schema mapping separated from domain classes (rejected)
  Rejected because: Imperative mapping decouples schema definitions from entity classes, doubling maintenance surface area and obscuring relationship declarations.
  When valid: Valid when domain entities must remain strictly decoupled from persistence frameworks.
- Raw SQL query execution with standalone data transfer structures (rejected)
  Rejected because: Bypasses automated dialect translation, increases susceptibility to SQL injection, and forfeits automated identity tracking and relationship cascade guarantees.
  When valid: Valid for specialized read-only query paths demanding extreme throughput.

## Risks

- Dialect variations in string length restrictions or JSON handling may cause schema generation failures on specific backends.
  Mitigation: Run automated dialect test suites against all supported relational backends on each schema change.
  Owner: Database Engineering Team
- Improper relationship cascade configurations may trigger unintentional deletion cascades or prevent row deletion.
  Mitigation: Mandate explicit cascade configurations and conduct query review for eager versus lazy relationship loading.
  Owner: Backend Architecture Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Define all primary and foreign key constraints explicitly in table arguments to ensure consistent constraint naming across dialects.
- Apply cross-dialect column variants on large text and structured fields to preserve index length budgets and dialect type fidelity.
- Ensure all relationship definitions explicitly declare back-population and orphan deletion strategies to prevent dangling rows.

## Continuation Context

Verify commands:

- Discover the static type checker execution command from project configuration and run it against the model definition modules to verify that all Mapped column definitions satisfy type contracts.
- Discover the test runner command from the repository build configuration and execute the data modeling test suite to validate database schema migrations and ORM relationship constraints.
- Discover the code linter command from project configuration and run it to verify compliance with declarative ORM style conventions.

Accept when:

- Static type analysis passes without errors or mismatches on Mapped attribute declarations and relationship mappings.
- Database model test executions pass across supported relational dialects without constraint violations or schema generation errors.
- All new and modified persistent entities inherit from the declarative base and declare columns using mapped_column.

## Enforcement

- Verified by: Automated static analysis checks verifying typed column annotations on all subclasses of the declarative base.
- Verified by: Automated schema migration tests running against all supported database dialect containers.
- Verified by: Peer review verification ensuring explicit relationship cascades and table argument constraints.
- Violation handling: Continuous integration failure on missing or improperly typed column declarations.
- Violation handling: Pull requests introducing untyped persistence models or missing table constraints will be blocked from merging.
- Violation handling: Flagged violations must be refactored to comply with typed declarative mapping rules before review sign-off.
- Exception process: Submit a technical justification detailing why a persistent entity cannot utilize the declarative base or mapped column syntax.
- Exception process: Obtain approval from the database architecture review team prior to merging custom schema structures.
- Exception process: Document dialect compatibility guarantees and lifecycle handling for any exempt persistence structure.
