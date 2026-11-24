---
version: 2
name: 6e2d3005-72cb-4fa8-b558-223810e0be4e-sqlalchemy-unified-data-access-and-session-management-architecture-relational-data-mutations-contained-within-explicit
description: "SQLAlchemy Unified Data Access and Session Management Architecture: Relational Data Mutations Contained Within Explicit"
metadata:
  actual:
    adr_id: 6e2d3005-72cb-4fa8-b558-223810e0be4e
    status: accepted
    candidate_group_id: 745cab4a-008b-4cc1-b804-ea11f419cbb4
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SQLAlchemy Unified Data Access and Session Management Architecture: Relational Data Mutations Contained Within Explicit

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- The system coordinates distributed workflow processing requiring durable state persistence, concurrent task updates, and administrative metadata queries across a relational datastore.
- The architecture supports multiple relational database backends, requiring a unified query abstraction that accommodates syntax divergence in joins, updates, and schema migrations.
- High-concurrency daemons and web API handlers frequently read and mutate shared task and DAG states, necessitating deterministic transaction boundaries and row-level locking controls.

## Problem Statement

Managing relational data persistence across multiple database engines without a cohesive query and transaction abstraction leads to dialect fragmentation, race conditions in concurrent job execution, and unoptimized relational queries that degrade system throughput.

## Decision

- **R-DAP-001** MUST: Relational data mutations MUST be contained within explicit transactional session boundaries terminated by explicit commit or rollback invocations.

## Policy Block

In scope:

- All backend services, REST API endpoints, background execution daemons, and database schema migrations interacting with the primary relational database.

Out of scope:

- In-memory analytical querying engines operating on decoupled object storage formats and external transient third-party service communications.

Exceptions:

- **EXC-30-001** Schema migration operations require bulk data transformations or structural DDL updates prior to entity model availability
  Approval: Database Architecture Team
  Documentation: Migration revision comment detailing cross-dialect execution paths and data validation

## Rationale

- Centralizing data access on SQLAlchemy ORM and Core statements provides a consistent relational abstraction layer that isolates business logic from underlying database engine differences across supported backends.
- Enforcing explicit session management, row-level pessimistic locking, and deterministic commit cycles prevents concurrent transaction collisions and maintains data integrity during distributed workflow execution.
- Applying eager loading and chunked query execution options prevents relationship traversal bottlenecks and bounds operational memory footprints across high-throughput data queries.

## Consequences

Positive:

- Uniform relational database interaction model across API services, background daemons, and schema migrations.
- Prevention of concurrency anomalies and data corruption through disciplined transaction scoping and row locking.
- Predictable query performance and memory utilization via explicit join strategies and chunked streaming.

Negative:

- Increased code complexity when authoring dialect-specific query branches for complex update or join constructs across distinct database engines.
- Potential deadlocks if row-level locking orders are not consistently structured across concurrent transaction handlers.

## Alternatives

- Direct database driver execution using raw parameterized SQL strings (rejected)
  Rejected because: Lacks cross-database dialect abstraction, bypasses ORM type safety and validation, and drastically increases vulnerability to SQL dialect divergence across supported backends.
  When valid: Lightweight single-engine utilities that do not require multi-dialect compatibility or complex entity relationship mappings.
- Fully detached active-record pattern with implicit auto-committing transactions (rejected)
  Rejected because: Hides transactional boundaries, leads to implicit session lifecycle leaks, and causes uncontrolled concurrency races in distributed execution environments.
  When valid: Small monolithic applications with low concurrency and simple single-row mutations.

## Risks

- Dialect divergence causing silent query failures on non-primary database backends when dialect-specific constructs are omitted.
  Mitigation: Implement multi-dialect automated integration test matrix verifying query generation and execution across all supported relational engines.
  Owner: Database Engineering Team
- Unintended memory exhaustion caused by unbounded query execution without streaming options on high-volume tables.
  Mitigation: Mandate pagination, result set limits, or yield_per execution options on all dynamic table query endpoints through automated checks.
  Owner: Backend API Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Structure database interactions within managed session contexts, ensuring session lifecycles are tied to the request or processing unit lifecycle and closed upon completion.
- When constructing complex queries involving multiple related models, apply eager loading options at statement definition time rather than relying on lazy relationship attributes.

## Continuation Context

Verify commands:

- Discover and run the project static analysis suite to verify that database access code adheres to SQLAlchemy statement conventions.
- Discover and execute the project integration test suite across all configured relational database engines to confirm query compatibility and transaction boundary integrity.

Accept when:

- All relational queries compile and execute successfully across all supported database dialect environments.
- Static checks pass with zero unmanaged session leaks, raw driver queries, or unhandled dialect branches.
- Integration test suites validate transaction isolation and row locking behavior under concurrent execution.

## Enforcement

- Verified by: Automated continuous integration test matrix running data access suites against all supported database engines.
- Verified by: Peer code review mandatory for changes introducing or modifying database queries, session handling, or migration scripts.
- Violation handling: Pull requests containing untracked raw SQL statements, missing transaction boundaries, or untested dialect branches are blocked from merging.
- Exception process: Exceptions must be submitted via an architectural review issue documenting the technical justification and cross-database impact analysis, requiring approval from the database architecture team.
