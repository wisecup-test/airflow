---
version: 2
name: 6d9300e4-e130-4897-9f25-2ae2e1c5459b-adoption-of-uuid6-for-uuidv7-time-ordered-surrogate-primary-keys-domain-logic-creating-child-entity-references
description: "Adoption of uuid6 for UUIDv7 Time-Ordered Surrogate Primary Keys: Domain Logic Creating Child Entity References"
metadata:
  actual:
    adr_id: 6d9300e4-e130-4897-9f25-2ae2e1c5459b
    status: accepted
    candidate_group_id: d4c4ea06-839a-45ad-b422-6bb9a0d0554e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of uuid6 for UUIDv7 Time-Ordered Surrogate Primary Keys: Domain Logic Creating Child Entity References

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- The persistence architecture requires globally unique, distributed surrogate primary keys for core domain models and transactional entities.
- Traditional random UUID algorithms cause index fragmentation and degradation of write performance across high-volume database tables.
- Schema migrations require consistent generation of time-ordered surrogate identifiers during table alterations and batch data backfills.

## Problem Statement

High-throughput database entities require globally unique primary keys that can be generated independently across distributed application processes without central coordination, while avoiding the catastrophic B-tree index fragmentation and performance degradation caused by non-sequential random UUID identifiers.

## Decision

- **R-LM-001** MAY: Domain logic creating child entity references prior to session flushing MAY directly invoke uuid6.uuid7 to obtain deterministic pre-persisted identity strings.

## Policy Block

In scope:

- All relational database entity models requiring surrogate primary keys.
- Schema migration scripts adding UUID columns or performing backfills on identifier fields.

Out of scope:

- Entities utilizing natural primary keys or composite domain keys where surrogate keys are explicitly excluded.
- Ephemeral in-memory data structures not persisted to relational database storage.

## Rationale

- Evidence across multiple core models demonstrates standardized usage of uuid6.uuid7 as the primary key default generator, ensuring sequential database insertion locality.
- Evidence across migration scripts reveals explicit batch routines generating time-ordered UUIDs to populate surrogate keys during schema transitions.
- Adopting a single time-ordered UUID generation standard prevents divergent identifier strategies and ensures consistent sorting semantics across distributed runtime environments.

## Consequences

Positive:

- Preserves database B-tree index locality by providing monotonically increasing, time-ordered identifier values.
- Enables decentralized, conflict-free primary key generation across distributed workers without central database sequence bottlenecks.
- Standardizes surrogate primary key typing and identifier generation patterns across relational entities and migration routines.

Negative:

- Introduces a third-party library dependency for surrogate identifier generation across core entity models and migrations.
- Time-ordered identifiers embed millisecond-level creation timestamps, leaking temporal creation metadata in externalized identifier strings.
- Requires database migration scripts to coordinate batch backfills when populating UUID columns across large legacy tables.

## Alternatives

- Native UUIDv4 random identifier generation (rejected)
  Rejected because: Random UUID generation causes severe B-tree page splits and index fragmentation in high-throughput relational database workloads.
  When valid: When identifier generation must be completely uncorrelated with creation timestamps for security or anonymity requirements.
- Centralized database sequential integer sequences (auto-increment) (rejected)
  Rejected because: Sequential integers require central database coordination, create migration bottlenecks, and leak entity volume information in distributed systems.
  When valid: When entity counts are bounded and single-node database instances operate without distributed or cross-node generation needs.

## Risks

- Incorrect column default binding could lead to identical timestamps or duplicate identifiers across rapid batch inserts.
  Mitigation: Ensure all model declarations configure column defaults with the callable reference rather than a pre-computed value, preserving per-row time precision.
  Owner: Engineering Team
- Divergent library versions across deployment environments could produce subtle behavior shifts in time component resolution.
  Mitigation: Maintain strict lock-file grounding to verify compatibility across target runtime environments.
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
- When declaring model primary keys, bind the column default directly to the callable function without invoking it at module import time.
- For batch database migrations, generate identifiers in controlled batch sizes to avoid transaction log exhaustion and table lock contention.

## Continuation Context

Verify commands:

- Discover and execute the project test runner across model and persistence test suites to verify surrogate key generation.
- Discover and run the project code linter and type checker to confirm compliance with uuid6 model default conventions.

Accept when:

- All relational entity models declaring UUID surrogate primary keys specify uuid6.uuid7 as the default generator.
- Database migration scripts executing backfills populate surrogate keys using time-ordered UUID generation consistent with uuid6.uuid7.
- Automated test suites pass without schema validation errors or primary key generation failures.

## Enforcement

- Verified by: Static analysis rules inspecting model primary key definitions for uuid6.uuid7 default assignments.
- Verified by: Automated code review and migration script checks ensuring backfills avoid non-sequential identifier generation.
- Verified by: Continuous integration test pipelines executing entity persistence cycles.
- Violation handling: Pull requests introducing non-compliant primary key generators or random UUID defaults are blocked by automated linters.
- Violation handling: Schema migrations using non-standard identifier backfill routines are rejected during review.
- Exception process: Exceptions must be reviewed by the architecture team with documented performance impact analysis on database index locality.
- Exception process: Temporary legacy exceptions must define a remediation plan to migrate to time-ordered UUID generation.
