---
version: 2
name: a55cef4f-8756-4f12-9ad8-160aaf0fffca-adopt-uuid-module-for-universal-entity-identification-across-persistence-and-api-boundaries-domain-entities-not-use-auto-incrementing
description: "Adopt uuid Module for Universal Entity Identification Across Persistence and API Boundaries: Domain Entities Not Use Auto Incrementing"
metadata:
  actual:
    adr_id: a55cef4f-8756-4f12-9ad8-160aaf0fffca
    status: accepted
    candidate_group_id: ff5dcc5e-8f7d-43df-bfc7-5e5ae3c9a01e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adopt uuid Module for Universal Entity Identification Across Persistence and API Boundaries: Domain Entities Not Use Auto Incrementing

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Surrogate keys and resource identifiers are defined and exchanged across persistence models, internal coordination APIs, and serialization layers.
- Distributed worker processes require the ability to instantiate new entities and execution traces autonomously without central sequence lock contention.
- FastAPI route definitions and Pydantic serialization models require strict type constraints to validate inputs and eliminate identifier parsing defects at API boundaries.
- Evidence across twenty-three files demonstrates consistent utilization of the uuid module and associated types for entity keys across core models and service routes.

## Problem Statement

Distributed execution components require globally unique, non-enumerable entity identifiers that can be generated independently without sequence bottlenecks while maintaining strict type safety across persistence mappings, API boundaries, and serialization schemas.

## Decision

- **R-LM-001** MUST_NOT: Domain entities MUST_NOT use auto-incrementing sequential integers or unvalidated arbitrary strings as surrogate primary keys across public API boundaries.

## Policy Block

In scope:

- Surrogate primary keys and foreign key definitions within database entity models.
- Identifier parameters declared in REST API route handlers.
- Entity representation fields in request and response serialization schemas.
- Identity subjects and resource references in execution authentication tokens.

Out of scope:

- User-defined natural keys with explicit business uniqueness constraints.
- Transient internal in-memory data structures not persisted or exposed via external APIs.

Exceptions:

- **EXC-20-001** External system integration contracts or legacy external references mandate non-UUID string formats.
  Approval: Architecture Review Board approval
  Documentation: Document legacy format mapping and validation rules in the relevant entity schema docstring.

## Rationale

- Evidence across twenty-three files demonstrates uniform adoption of UUID typing for primary keys, foreign keys, route parameters, and data models across core modules.
- Using strongly-typed UUID values directly in route path parameters enables automatic framework-level validation, rejecting malformed identifiers prior to database querying.
- Decentralized UUID generation enables distributed workers and schedulers to mint entity keys without centralized database lock contention or sequential collision risks.

## Consequences

Positive:

- Decentralized and collision-free identifier generation across distributed execution nodes without coordination bottlenecks.
- End-to-end type safety spanning database persistence definitions, API routing parameters, and serialization schemas.
- Elimination of predictable resource identifier enumeration vulnerabilities across public and execution API endpoints.
- Optimized database index locality when adopting time-ordered UUID variants for high-throughput write workloads.

Negative:

- Higher storage consumption in database tables and indexes compared to standard integer identifiers.
- Increased parsing and string formatting overhead during serialization across service boundaries.
- Reduced human readability in debug logs and ad-hoc database queries compared to compact sequential numbers.

## Alternatives

- Auto-incrementing sequential integer identifiers (rejected)
  Rejected because: Exposes public API endpoints to enumeration vulnerabilities and causes sequence contention across distributed execution worker nodes.
  When valid: Applicable only for strictly isolated, high-volume append logs within private internal tables that never cross service or API boundaries.
- Arbitrary string identifiers and unvalidated slug tokens (rejected)
  Rejected because: Lacks standardized format enforcement, increases storage fragmentation, and bypasses static type verification across schema boundaries.
  When valid: Applicable for user-defined human-readable aliases such as custom DAG display identifiers.

## Risks

- Database B-tree index fragmentation resulting from random UUID generation in high-frequency insertion tables.
  Mitigation: Adopt time-ordered UUID variants such as uuid6.uuid7 for high-write tables to preserve sequential index insertion locality.
  Owner: engineering team
- Inconsistent serialization formats when exchanging raw binary versus canonical string UUID representations across client boundaries.
  Mitigation: Enforce strict UUID typing across serialization layers to ensure consistent string formatting at boundary transitions.
  Owner: engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Configure object-relational model identifier columns with native database UUID types to maintain storage efficiency and index optimization.
- Declare path parameters and response schema attributes using the UUID type to ensure automatic input validation and serialization consistency across endpoints.

## Continuation Context

Verify commands:

- Discover the project test runner and execute the automated test suite covering data model persistence and API schema serialization.
- Discover the repository static type analysis tool and run it across data models and route definitions to verify UUID typing compliance.

Accept when:

- All entity identifier attributes in data models and serialization schemas pass type checking with UUID typing.
- API route handlers validate input identifier path parameters as UUID instances and serialize them to standard UUID string representations.
- Database column specifications for primary and foreign entity keys use native UUID mappings without unvalidated string fallbacks.

## Enforcement

- Verified by: Automated static type analysis integrated into the continuous integration pipeline to verify UUID typing.
- Verified by: Schema validation checks confirming identifier column definitions in database migration manifests.
- Verified by: Code review verification ensuring route parameters and response schemas declare explicit UUID types.
- Violation handling: Automated pull request check failures when entity identifiers are declared as untyped values or generic strings.
- Violation handling: Rejection of database migrations that introduce auto-incrementing integer surrogate keys for core domain entities.
- Exception process: Submit an architectural exception request detailing the technical or external integration constraint requiring an alternative identifier structure.
- Exception process: Obtain approval from the technical architecture review team before committing non-UUID entity identifiers.
- Exception process: Document the non-standard identifier rationale and validation mapping directly within the module documentation.
