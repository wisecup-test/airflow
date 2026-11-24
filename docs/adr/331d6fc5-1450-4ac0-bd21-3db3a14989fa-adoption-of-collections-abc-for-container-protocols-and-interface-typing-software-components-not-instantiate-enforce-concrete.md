---
version: 2
name: 331d6fc5-1450-4ac0-bd21-3db3a14989fa-adoption-of-collections-abc-for-container-protocols-and-interface-typing-software-components-not-instantiate-enforce-concrete
description: "Adoption of collections.abc for Container Protocols and Interface Typing: Software Components Not Instantiate Enforce Concrete"
metadata:
  actual:
    adr_id: 331d6fc5-1450-4ac0-bd21-3db3a14989fa
    status: accepted
    candidate_group_id: 76cf2bee-4161-45f8-a33b-5b27b528a00c
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - hatch_build.py
      - src/airflow/**
---

# Adoption of collections.abc for Container Protocols and Interface Typing: Software Components Not Instantiate Enforce Concrete

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `hatch_build.py`
- `src/airflow/**`

## Context

- The system coordinates task scheduling, workload execution, state tracking, and API serialization across numerous distributed components.
- Data structures representing collections of task instances, event logs, execution workloads, and configuration parameters flow across module boundaries.
- Coupling function signatures and response schemas to concrete container types limits lazy evaluation, increases memory consumption, and impairs interoperability.

## Problem Statement

Coupling cross-boundary interfaces and data models to concrete collection implementations prevents memory-efficient streaming, restricts callers from supplying specialized collection variants, and causes redundant in-memory materialization of large dataset responses.

## Decision

- **R-LM-001** SHOULD_NOT: Software components SHOULD NOT instantiate or enforce concrete collection subclass hierarchies when abstract container protocols satisfy functional requirements.

## Policy Block

In scope:

- All public API route definitions, request bodies, and response serialization models.
- All core execution workloads, state stores, and task orchestration interfaces exchanging collections of domain objects.
- Runtime type introspection guards that verify collection and mapping behaviors.

Out of scope:

- Low-level database driver bindings where query drivers require specific concrete primitive types.
- Third-party library integrations whose rigid signatures explicitly enforce concrete collection classes.

## Rationale

- Utilizing collections.abc container protocols ensures public contracts remain agnostic to underlying storage and evaluation strategies, supporting both generators and concrete buffers.
- Runtime protocol introspection using abstract base classes reliably distinguishes between mappings, sequences, and generic iterables without brittle inspection of class hierarchies.
- Consistent adherence across data models and execution workloads guarantees standard collection ergonomics across distributed orchestration modules.

## Consequences

Positive:

- Decouples core API contracts, execution workloads, and data models from concrete collection implementations, allowing streaming generation and memory-efficient iterators.
- Enables polymorphic runtime introspection where domain entities can validate protocol compliance without forcing specific class inheritance hierarchies.
- Maintains uniform interface declarations across route handlers, data serializers, and worker execution payloads.

Negative:

- Requires software engineers to distinguish between sequence, mapping, and iterable semantics rather than defaulting to concrete collection types.
- Developers must exercise care with single-pass iterables to prevent unintended multi-pass consumption bugs.

## Alternatives

- Coupling method signatures and data transfer schemas directly to concrete built-in collection classes (rejected)
  Rejected because: Restricts client flexibility, prevents lazy evaluation and streaming generators, and forces memory allocation for full materialization of collection payloads.
  When valid: Valid in isolated micro-benchmarks or low-level performance-critical operations requiring direct memory layout access.
- Utilizing generic container aliases from the legacy typing module instead of collections.abc (rejected)
  Rejected because: Introduces redundancy with runtime abstract container protocols and diverges from modern ecosystem standards deprecating container aliases in favor of standard abstract base classes.
  When valid: Valid only when targeting deprecated runtime environments where standard abstract base classes do not support parameterized subscriptions.

## Risks

- Consuming single-pass iterators across multiple independent downstream operations can result in silent empty iteration errors.
  Mitigation: Document multi-pass expectations on function contracts and materialize collections internally if multiple iterations are required.
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
- When defining response payloads that yield series of records, prefer iterable abstractions over materialized collections to preserve streaming capabilities.
- When performing runtime capability checks, verify behavior using abstract base class introspection rather than querying concrete class ancestry.

## Continuation Context

Verify commands:

- Discover and execute the project static type analysis test suites to verify that all container annotations adhere to abstract container protocols.
- Discover and execute the project contract test suites to verify serialization and runtime validation of abstract collection payloads.

Accept when:

- Static type analysis passes across all public modules, data models, and API interfaces without container type mismatches.
- Runtime test suites pass when receiving custom iterable or mapping implementations adhering to collections.abc protocols.

## Enforcement

- Verified by: Automated static type analysis in the continuous integration pipeline verifying that signatures use abstract container protocols.
- Verified by: Peer code review ensuring new public API models and internal contracts utilize collections.abc interfaces.
- Violation handling: Continuous integration job failure for non-compliant container annotations or concrete runtime type assertions.
- Violation handling: Rejection of pull requests introducing concrete collection constraints on public or cross-boundary interfaces.
- Exception process: Submit an architectural exception request detailing technical necessity and profiling data justifying concrete container coupling.
- Exception process: Obtain formal review and sign-off from the technical steering group before merging exceptions.
