---
version: 2
name: 85b461e6-bad5-456c-97bd-1793ef740404-weakref-module-adoption-for-cycle-free-hierarchical-reference-management-components-managing-stream-buffers-external-resource
description: "weakref Module Adoption for Cycle-Free Hierarchical Reference Management: Components Managing Stream Buffers External Resource"
metadata:
  actual:
    adr_id: 85b461e6-bad5-456c-97bd-1793ef740404
    status: accepted
    candidate_group_id: 4e7616c0-aaf6-46f8-85d7-4d2f47aa6208
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# weakref Module Adoption for Cycle-Free Hierarchical Reference Management: Components Managing Stream Buffers External Resource

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Hierarchical execution models and graph serialization structures maintain complex parent-child relationships where child nodes require programmatic access to their parent containers.
- Direct bidirectional strong references between parent graph nodes and child task nodes create cyclic reference graphs that defeat deterministic reference-count deallocation and force reliance on periodic cyclic garbage collection.
- Accumulators and streaming middleware coordinate transient resources across asynchronous lifespans, where unintended strong reference retention risks memory accumulation.

## Problem Statement

Maintaining bidirectional links in hierarchical graph definitions and long-lived stream accumulators creates reference cycles that prevent prompt object deallocation, increase memory pressure, and complicate tree traversal. The architecture requires a standard mechanism to maintain parent access and resource references without retaining ownership or preventing garbage collection.

## Decision

- **R-LM-001** SHOULD: Components managing stream buffers or external resource handles SHOULD utilize weak reference callbacks or finalizers when deterministic context-manager scoping cannot be guaranteed.

## Policy Block

In scope:

- Hierarchical graph data structures with bidirectional parent-child navigation
- Resource accumulators and streaming buffers attached to long-running application runtimes

Out of scope:

- Unidirectional data structures where child entities maintain no backward reference to their parent
- Immutable value objects and serialized transfer payloads that do not retain parent pointers

## Rationale

- Using weakref.proxy for parent references breaks reference cycles across node hierarchies, ensuring that child tasks do not artificially prolong the memory lifespan of parent groups.
- Empirical evidence across serialization definitions demonstrates that weakref.proxy enables natural attribute access syntax while delegating lifecycle ownership strictly to the root container.
- Employing weak reference mechanisms in log stream accumulators and application runtime contexts limits the blast radius of uncollected transient objects across long-lived processes.

## Consequences

Positive:

- Eliminates memory leaks induced by cyclic reference graphs in hierarchical structures.
- Allows child nodes to transparently access parent container properties without manual cleanup teardown sequences.
- Reduces overhead on runtime cyclic garbage collection passes in high-throughput daemon processes.

Negative:

- Accessing a weakref proxy after referent deallocation raises runtime exceptions if lifecycle boundaries are misaligned.
- Weak references cannot be attached to certain built-in types without subclassing or container wrappers.

## Alternatives

- Bidirectional Strong References with Explicit Manual Teardown (rejected)
  Rejected because: Manual dissociation of parent-child pointers is error-prone, violates encapsulation, and causes memory leaks when exceptions interrupt traversal or teardown.
  When valid: Valid only in small, self-contained scopes where object lifespans are strictly bounded by synchronous execution blocks.
- Identifier-Based Indirection with Centralized Lookup Registry (rejected)
  Rejected because: Resolving parent entities via external registry lookups adds hash-map overhead to frequent property evaluations and requires global registry state management.
  When valid: Valid across distributed process boundaries or relational persistence boundaries where in-memory object pointer traversal is impossible.

## Risks

- Premature referent deallocation leading to reference errors when traversing weak proxies.
  Mitigation: Ensure root containers maintain authoritative strong ownership for the entire duration of the traversal lifecycle.
  Owner: Core Framework Engineering Team
- Incompatibility of weak references with certain primitive or third-party immutable types.
  Mitigation: Wrap non-weak-referenceable primitives in dedicated composite node classes that support weak referencing.
  Owner: Core Framework Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Assign weakref.proxy to back-reference attributes during child node registration to enable transparent attribute access on parent instances without holding strong references.
- Audit serialization boundaries to confirm weak proxies are reconstructed or resolved into structural identifiers prior to inter-process transmission.

## Continuation Context

Verify commands:

- Discover the project test execution runner from repository configuration and execute the unit test suite covering graph serialization and reference cycle deallocation.
- Discover the repository static analysis linter and run validation across core modules to verify compliance with memory and reference lifecycle conventions.

Accept when:

- All graph traversal and parent resolution tests pass without triggering reference exceptions on weakly referenced parent objects.
- Object graphs with bidirectional parent-child links release memory upon parent dereferencing without requiring explicit cyclic garbage collection sweeps.

## Enforcement

- Verified by: Automated test suites validating memory deallocation and parent resolution
- Verified by: Peer code reviews auditing back-reference implementations in structural components
- Violation handling: Pull requests introducing strong circular references in hierarchical structures will be blocked during review
- Violation handling: Memory accumulation detections during stress testing require refactoring to weak references before release
- Exception process: Exceptions require documented architecture review demonstrating that lifecycle boundaries guarantee simultaneous parent-child destruction without cycles.
