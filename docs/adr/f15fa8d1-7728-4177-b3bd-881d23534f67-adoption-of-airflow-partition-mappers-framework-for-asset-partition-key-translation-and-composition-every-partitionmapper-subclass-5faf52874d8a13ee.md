---
version: 2
name: f15fa8d1-7728-4177-b3bd-881d23534f67-adoption-of-airflow-partition-mappers-framework-for-asset-partition-key-translation-and-composition-every-partitionmapper-subclass-5faf52874d8a13ee
description: "Adoption of airflow.partition_mappers Framework for Asset Partition Key Translation and Composition: Every Partitionmapper Subclass Implement Downstream Method"
metadata:
  actual:
    adr_id: f15fa8d1-7728-4177-b3bd-881d23534f67
    status: accepted
    candidate_group_id: 5fbc0798-2328-4d47-a466-b9fe47a7755f
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of airflow.partition_mappers Framework for Asset Partition Key Translation and Composition: Every Partitionmapper Subclass Implement Downstream Method

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Airflow-managed data assets frequently depend on upstream assets that employ disparate partitioning schemes, such as region prefixes, date granularities, or categorical segments.
- Downstream assets need deterministic mechanisms to map upstream partition keys to downstream keys without embedding repetitive string parsing logic across pipeline definitions.
- Distributed scheduling environments require partition translation logic to be fully serializable for cross-node dispatch and persistent state tracking.
- Extensibility requirements demand that custom partition transformations can be implemented by end users and discovered by asset schedulers without modifying core library code.

## Problem Statement

Data pipelines frequently require translating partition keys across heterogeneous asset boundaries, such as collapsing regional prefixes, filtering permissible partitions, or combining orthogonal dimensions into compound keys. Without a formal abstraction, partition mapping logic becomes fragmented, unvalidated, and incapable of safe serialization across distributed scheduling and execution boundaries.

## Decision

- **R-LM-001** MUST: Every PartitionMapper subclass MUST implement the to_downstream method to map an upstream partition key string to a downstream partition key string or an iterable of downstream partition key strings.

## Policy Block

In scope:

- All partition key translation logic between upstream and downstream partitioned assets.
- Composition of sequential and multi-dimensional partition key mappings.
- Plugin-based custom partition mapper extensions.

Out of scope:

- Unpartitioned data asset dependencies and schedules.
- Direct runtime execution of data transformation payloads within tasks.

Exceptions:

- **EXC-20-001** Legacy assets operating without partitioned asset timetables require raw string identity passthrough without mapper overhead.
  Approval: Architecture Review Board
  Documentation: Document exception in the asset configuration metadata.

## Rationale

- The airflow.partition_mappers architecture provides a polymorphic, serializable contract (PartitionMapper) that standardizes partition key transformations across the workflow engine.
- Composite mappers such as ChainMapper and ProductMapper enable arbitrary pipeline topology expressions through composition rather than custom monolithic logic.
- Embedding serialize and deserialize contracts directly in the mapper protocol guarantees distributed workers and schedulers can serialize mapping configurations without state loss.
- Providing a formal plugin registration interface allows pipeline authors to introduce domain-specific partition mapping logic while preserving native scheduler compatibility.

## Consequences

Positive:

- Standardizes partition key translation across core and custom asset dependencies via a unified PartitionMapper interface.
- Enables modular composition of complex partition workflows through ChainMapper and ProductMapper abstractions.
- Ensures safe serialization and deserialization across distributed execution nodes.
- Allows third-party and user-defined mappers to integrate seamlessly via the plugin partition mapper registry.

Negative:

- Requires boilerplate serialize and deserialize implementations for every new custom partition mapper.
- Introduces intermediate object allocation overhead compared to raw procedural string manipulations.
- Requires careful handling of temporal anchors when combining categorical and time-based partition dimensions.

## Alternatives

- Ad-hoc inline string manipulation in asset definitions (rejected)
  Rejected because: Inlining partition key mapping logic inside asset schedules scatters transformation rules across definitions, duplicates string manipulation code, and prevents serialization across execution boundaries.
  When valid: Only viable in single-asset proof-of-concept workflows with static, non-composed partition keys.
- External configuration tables or database mapping registries (rejected)
  Rejected because: Maintaining external mapping tables introduces operational dependencies and network latency to key resolution, whereas PartitionMapper provides deterministic, lightweight in-memory translations.
  When valid: Appropriate when partition relationships change dynamically at runtime independently of asset definitions.
- Monolithic procedural mapper functions (rejected)
  Rejected because: Procedural functions lack a standardized contract for serialization, deserialization, plugin extensibility, and temporal anchor extraction.
  When valid: Acceptable for one-off script executions where state persistence and cross-process distribution are unnecessary.

## Risks

- Ambiguity in temporal anchor recovery when composing multi-dimensional partition mappers
  Mitigation: Enforce strict conventions in ProductMapper to return None when multiple temporal dimensions or mismatching segment counts are encountered.
  Owner: engineering team
- Serialization payload drift or backward incompatibility across distributed scheduler and worker nodes
  Mitigation: Require bidirectional serialization tests for all custom and core partition mapper implementations.
  Owner: engineering team
- Performance overhead or recursion issues in deeply nested ChainMapper hierarchies
  Mitigation: Impose validation on mapper depth and enforce max_downstream_keys thresholds on composite mappers.
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
- Subclasses of PartitionMapper must implement to_downstream to accept a string key and return a string key or an iterable of string keys.
- When implementing serialize and deserialize methods, encode and decode nested child mappers using the established serialization utilities to maintain state integrity.
- Temporal partition resolution must be explicitly defined using to_partition_date or carry_partition_date when downstream scheduling depends on temporal anchors.

## Continuation Context

Verify commands:

- echo "Discover and execute the repository test runner targeting partition mapper classes"
- echo "Discover and execute the repository type validation suite against PartitionMapper contracts"
- echo "Discover and execute serialization roundtrip tests for custom and composite partition mappers"

Accept when:

- All unit and integration tests for PartitionMapper implementations pass without failures.
- Every PartitionMapper subclass successfully executes serialize and deserialize roundtrips preserving all internal parameters.
- ChainMapper and ProductMapper composite hierarchies resolve downstream keys and temporal partition dates consistently across configured dimensions.

## Enforcement

- Verified by: Automated unit and integration test suites validating partition mapper contracts and serialization fidelity.
- Verified by: Pull request code reviews ensuring new partition mappers adhere to PartitionMapper inheritance and serialization protocols.
- Violation handling: Pull requests lacking required serialization methods or contract conformance will be rejected during automated checks.
- Violation handling: Non-compliant partition translation implementations discovered in production will be flagged for refactoring.
- Exception process: Exceptions must be submitted via an architecture review request detailing the technical blocker and migration roadmap.
