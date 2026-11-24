---
version: 2
name: 65a5abb5-851c-443c-b791-60dc1055d172-adoption-of-collections-abc-for-collection-protocols-and-abstract-container-interfaces-internal-utility-implementations-employ-runtime-protocol
description: "Adoption of collections.abc for Collection Protocols and Abstract Container Interfaces: Internal Utility Implementations Employ Runtime Protocol"
metadata:
  actual:
    adr_id: 65a5abb5-851c-443c-b791-60dc1055d172
    status: accepted
    candidate_group_id: e0b68e13-b0f2-43e5-baa4-7d1575062d00
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/**
---

# Adoption of collections.abc for Collection Protocols and Abstract Container Interfaces: Internal Utility Implementations Employ Runtime Protocol

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`
- `src/tests_common/**`

## Context

- Shared testing utilities, mock executors, and documentation extensions require uniform collection contracts across diverse execution contexts.
- Direct dependency on concrete container implementations creates tight coupling between internal testing fixtures and production execution engines.
- Standard abstract base classes establish structural protocol validation while supporting varied concrete container implementations across independent subpackages.

## Problem Statement

Coupling shared interfaces, mock execution environments, and utility functions to concrete collection implementations limits runtime flexibility, complicates mock data substitution, and obscures structural contract requirements across shared development packages.

## Decision

- **R-LM-001** SHOULD: Internal utility implementations SHOULD employ runtime protocol inspection against collections.abc classes when validating input objects for expected collection semantics.

## Policy Block

In scope:

- Shared development modules, testing utilities, mock fixtures, and documentation extensions handling collection data structures.
- Public and internal interface definitions that accept or process iterable, sequence, or mapping parameters.

Out of scope:

- Third-party boundary adapters where external library contracts mandate concrete container types.
- Performance-critical inner execution loops requiring direct low-level container memory layouts.

Exceptions:

- **EXC-20-001** External client library signatures strictly require concrete container types without abstract protocol support.
  Approval: Architecture review board and core maintainers
  Documentation: Document the concrete container requirement in the calling module docstring referencing the upstream boundary constraint.

## Rationale

- Standardizing on collections.abc separates interface definitions from underlying data structure choices, facilitating polymorphically interchangeable test mocks and production components.
- Abstract collection protocols prevent runtime failures caused by caller pass-through of read-only sequences, generator streams, or specialized mappings.
- Consistent usage across ten shared modules proves that standard container protocols provide sufficient structural guarantees without unnecessary operational coupling.

## Consequences

Positive:

- Subsystems accept broader ranges of conforming collection structures, increasing modularity and testability.
- Mock implementations and test fixtures can substitute custom collection wrappers without altering consuming code.
- Interface declarations explicitly communicate required container capabilities through standard protocol abstractions.

Negative:

- Engineers must deliberately evaluate and select appropriate abstract container protocols rather than defaulting to concrete container types.
- Runtime validation requires explicit protocol inspection rather than direct type equality checks.

## Alternatives

- Coupling function signatures directly to concrete container implementations across shared utilities (rejected)
  Rejected because: Concrete container coupling restricts callers from providing alternative collection types, generator pipelines, or custom test doubles.
  When valid: When internal data structures are strictly encapsulated within private function scopes and never exposed across module boundaries.
- Relying solely on informal duck typing without standard abstract base class declarations (rejected)
  Rejected because: Informal duck typing lacks uniform runtime validation hooks and produces inconsistent static analysis protocol checks across modular packages.
  When valid: When integrating with dynamic legacy external libraries that do not conform to standard abstract base classes.

## Risks

- Inappropriate protocol selection where a function requires sequence indexing but specifies an iterable protocol.
  Mitigation: Establish code review guidelines and automated static analysis to ensure annotated protocols match container operations performed within function bodies.
  Owner: Engineering team
- Subtle semantic differences between concrete container methods and minimal abstract base class interfaces.
  Mitigation: Validate test doubles and mock collections against the full functional requirements of consuming components.
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
- Inspect existing function signatures and replace concrete container parameter types with corresponding abstract base classes from collections.abc.
- Ensure mock objects and test utilities implementing container semantics subclass or conform to the relevant collections.abc interface.

## Continuation Context

Verify commands:

- Discover and run the repository static type checker to verify that collection annotations conform to collections.abc protocols.
- Discover and execute the repository test runner across shared utility and test framework test suites to validate collection compatibility.

Accept when:

- Static type analysis across all shared modules completes with zero protocol incompatibility errors.
- All unit and integration test suites utilizing mock containers and collection handlers pass without interface errors.

## Enforcement

- Verified by: Continuous integration automated static type checking pipelines.
- Verified by: Peer code review verification during pull request inspection.
- Violation handling: Automated pull request check failures blocking merge when concrete container parameters violate abstraction standards.
- Violation handling: Required code refactoring during review before merging into protected branches.
- Exception process: Submit an architectural review request with documented technical rationale explaining why an external boundary requires concrete container coupling.
