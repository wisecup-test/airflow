---
version: 2
name: af5190b2-a6ad-4fe0-96af-df21eb9cc493-serializeddagmodel-payload-hashing-and-integrity-verification-serialized-payload-comparison-not-rely-direct
description: "SerializedDagModel Payload Hashing and Integrity Verification: Serialized Payload Comparison Not Rely Direct"
metadata:
  actual:
    adr_id: af5190b2-a6ad-4fe0-96af-df21eb9cc493
    status: accepted
    candidate_group_id: 1e964b15-9872-41c3-836f-d17c05317f56
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SerializedDagModel Payload Hashing and Integrity Verification: Serialized Payload Comparison Not Rely Direct

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Workflow orchestration engines serialize execution graphs and schedule definitions into structured payloads that are persisted in shared database storage for lightweight retrieval by operational components.
- Non-deterministic dictionary key ordering and variable nesting in serialized JSON structures cause identical logical definitions to produce conflicting byte representations.
- Changes in file locations, bundle references, or ambient execution environments introduce non-semantic variance into serialized objects, risking spurious version increments and database write amplification.
- A deterministic fingerprinting mechanism is required to verify payload integrity and reliably determine whether an update represents an authentic structural change.

## Problem Statement

Without canonical normalization and deterministic hashing of serialized workflow payloads, trivial differences in key sorting or deployment paths trigger unnecessary database writes, version recreation, and cache invalidation, compromising persistence performance and state consistency across distributed service boundaries.

## Decision

- **R-A-001** MUST_NOT: Serialized payload comparison MUST_NOT rely on direct string serialization or unsorted dictionary equality without canonical structure sorting.

## Policy Block

In scope:

- Serialization and persistence of workflow models containing structured task and dependency definitions.
- Version comparison and state synchronization between processing workers and persistent database storage.

Out of scope:

- Transient in-memory execution states that do not undergo database persistence or version tracking.
- Unserialized dynamic workflow definitions prior to structured schema transformation.

## Rationale

- Canonical recursive key sorting guarantees that logically equivalent serialized structures always evaluate to identical digest values regardless of dictionary insertion order.
- Excluding transient location attributes from the digest computation isolates workflow definition identity from volatile filesystem and packaging details.
- Prefetching existing model hashes and performing digest comparisons reduces database lock contention and write operations across recurring synchronization cycles.

## Consequences

Positive:

- Eliminates redundant database writes and unnecessary version records for structurally unchanged workflow definitions.
- Guarantees deterministic identity and integrity verification for persisted serialized models across distributed services.
- Improves operational scalability by permitting lightweight database reads without importing and executing source definitions.

Negative:

- Incurs computational overhead during ingestion for recursive sorting, attribute filtering, and cryptographic digest calculation.
- Requires strict synchronization between serialization schema updates and hashing routines to prevent false positive changes.

## Alternatives

- Direct raw JSON string comparison without recursive key sorting (rejected)
  Rejected because: Dictionary serialization without strict deterministic key ordering produces unstable hashes for identical structural definitions.
  When valid: Only viable when serialized payloads are strictly guaranteed to maintain identical key order across all serialization runtimes.
- Timestamp-based write invalidation without content hashing (rejected)
  Rejected because: Timestamp comparisons trigger false update cycles on file touches without detecting actual structural modifications.
  When valid: Acceptable only in ephemeral test harnesses where persistence churn carries no performance or versioning consequence.

## Risks

- Deeply nested or exceptionally large serialized definitions may experience noticeable serialization latency during recursive sorting.
  Mitigation: Optimize dictionary sorting routines, apply depth boundaries, and utilize efficient hashing algorithms over normalized payloads.
  Owner: Core Architecture Team
- Accidental exclusion of semantically significant fields from hashing could mask genuine workflow definition updates.
  Mitigation: Enforce automated regression tests comparing mutated model definitions against baseline hashes to ensure every functional attribute is tracked.
  Owner: Persistence Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure that SerializedDagModel.hash implementations strip transient bundle and location fields from a shallow copy of the normalized dictionary rather than modifying the original in-memory data structure in place.
- Integrate payload hashing directly with database persistence write hooks so that pre-write checks evaluate hash equality prior to initiating transactions.

## Continuation Context

Verify commands:

- Discover and run the repository test target verifying model hashing, serialization normalization, and change detection logic.
- Discover and execute the project database integration suite to validate write avoidance when persisting unchanged serialized payloads.

Accept when:

- Model hashing consistently yields identical digests for identical payloads across variable dictionary orderings.
- Persistence write routines detect unchanged digests and skip database update operations.

## Enforcement

- Verified by: Automated continuous integration suites testing model serialization and database synchronization routines.
- Verified by: Peer code review verifying compliance with canonical sorting and payload hashing constraints.
- Violation handling: Pull requests omitting deterministic hashing or bypassing pre-write change detection checks are rejected by continuous integration.
- Violation handling: Direct model updates bypassing digest generation are flagged as architecture violations during code inspection.
- Exception process: Exceptions for non-hashed ephemeral models require formal review and approval by the architecture and persistence leads with documented rationale.
