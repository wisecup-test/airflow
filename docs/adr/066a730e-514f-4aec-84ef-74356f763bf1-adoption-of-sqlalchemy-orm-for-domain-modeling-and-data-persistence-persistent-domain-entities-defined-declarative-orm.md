---
version: 2
name: 066a730e-514f-4aec-84ef-74356f763bf1-adoption-of-sqlalchemy-orm-for-domain-modeling-and-data-persistence-persistent-domain-entities-defined-declarative-orm
description: "Adoption of SQLAlchemy ORM for Domain Modeling and Data Persistence: Persistent Domain Entities Defined Declarative Orm"
metadata:
  actual:
    adr_id: 066a730e-514f-4aec-84ef-74356f763bf1
    status: accepted
    candidate_group_id: 7b52cffe-2b32-4abf-8983-3fb04b7fe068
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of SQLAlchemy ORM for Domain Modeling and Data Persistence: Persistent Domain Entities Defined Declarative Orm

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Static code analysis identifies persistent domain models and API service routers depending uniformly on SQLAlchemy and SQLAlchemy ORM modules for data modeling and query operations.
- Database models require standardized declarative column definitions, primary and foreign key constraints, and explicit relationship mappings to prevent schema drift.
- API endpoints and background task services require consistent query composition, filtering, sorting, and eager loading mechanisms to maintain optimal database performance.

## Problem Statement

As service endpoints and domain operations scale, fragmented data access patterns, ad-hoc query composition, and uncoordinated object hydration cause connection leaks, schema drift, and severe query performance degradation. Without a unified persistence and object-relational mapping standard, models lack type-safe schema declarations and endpoints risk executing repetitive queries across relational associations.

## Decision

- **R-LM-001** MUST: Persistent domain entities MUST be defined using declarative ORM base classes with explicit mapped type annotations and declarative column mappings.

## Policy Block

In scope:

- Relational database schema modeling, entity relationship definitions, and persistence operations.
- API route handlers, service components, and background tasks executing queries against the relational store.

Out of scope:

- Non-relational data storage mechanisms, ephemeral in-memory state caches, and external message broker integrations.
- Static configuration files and metadata stores that do not require relational persistence.

## Rationale

- Static code analysis reveals consistent reliance on SQLAlchemy ORM across persistence models and API routing components for declarative schema mapping and relational operations.
- Centralizing on a single object-relational mapping abstraction provides uniform type annotations, consistent relationship navigation, and unified session management across decoupled modules.
- Explicit relationship loading strategies mitigate latent query performance degradation and connection pool saturation under high-concurrency API workloads.

## Consequences

Positive:

- Ensures unified entity modeling conventions and cohesive relationship mappings across disparate functional boundaries.
- Reduces latency and connection overhead by enabling fine-grained relationship loading configurations.
- Enhances compile-time and static analysis checks through typed column mappings and declarative query constructs.

Negative:

- Introduces an abstraction layer over native database engines that requires team familiarity with ORM session mechanics and identity maps.
- Increases memory consumption when hydrating large result sets into full ORM entity instances rather than raw tuples.
- Imposes architectural discipline to avoid silent query amplification through improperly configured relationship lazy loading.

## Alternatives

- Raw SQL queries and native database driver cursors (rejected)
  Rejected because: Omits declarative schema definitions, increases the risk of injection vulnerabilities, and duplicates query construction logic across service layers.
  When valid: When executing isolated high-throughput analytical ingestion workloads that bypass domain entity logic.
- Lightweight query builders without full ORM entity state tracking (rejected)
  Rejected because: Lacks built-in relationship lazy-loading guards, unit-of-work transaction tracking, and typed model metadata needed across domain services.
  When valid: When building minimal microservices with strictly read-only reporting access.

## Risks

- Unintentional query amplification from deferred loading across relational associations during response serialization.
  Mitigation: Require explicit query loading strategies and relationship access constraints on all endpoint response paths.
  Owner: Engineering Team
- Session leak or uncommitted transaction state causing connection pool exhaustion.
  Mitigation: Enforce automated session scoping through dependency injection or contextual session managers.
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
- Define database models by subclassing the shared declarative base and specifying typed attributes with mapped column definitions.
- When constructing complex queries that join multiple related tables, apply explicit joined or selectin loading options to optimize fetch performance.
- Ensure all transactional writes and session commits occur within designated service boundaries rather than directly within model property accessors.

## Continuation Context

Verify commands:

- Discover and run the project static analysis and type check validation scripts to confirm ORM mapping compliance.
- Discover and execute the persistence test suite to verify database schema creation, relationship loading, and transaction rollbacks.
- Discover and run the database migration validation scripts to verify schema constraints against target engines.

Accept when:

- All entity mappings pass static type checking and declarative model validation without schema errors.
- Relational queries demonstrate deterministic query execution counts and explicit eager loading configurations in integration tests.
- All persistence transactions complete cleanly without orphaned database connections or unresolved session states.

## Enforcement

- Verified by: Automated continuous integration checks executing repository test suites and static analysis linters.
- Verified by: Mandatory peer code reviews validating that new persistence logic adheres to ORM mapping and query loading conventions.
- Violation handling: Pull requests introducing unapproved raw SQL queries or unconfigured relationship traversals are blocked by automated review gates.
- Violation handling: Violations identified in post-merge audits must be remediated in a dedicated refactoring change.
- Exception process: Exceptions for specialized performance-critical queries must be formally proposed with benchmark evidence and approved by technical architecture leads.
