---
version: 2
name: 963045ac-0be3-47d4-8322-6611c12b9996-collections-abc-interface-and-container-protocol-adoption-components-accepting-ordered-indexed-collections-specify
description: "collections.abc Interface and Container Protocol Adoption: Components Accepting Ordered Indexed Collections Specify"
metadata:
  actual:
    adr_id: 963045ac-0be3-47d4-8322-6611c12b9996
    status: accepted
    candidate_group_id: 13aa5576-65d0-44ed-826a-b8ce18720c76
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/utils/**
      - mypy/src/airflow_mypy/plugins/**
---

# collections.abc Interface and Container Protocol Adoption: Components Accepting Ordered Indexed Collections Specify

Date: 2025-02-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/utils/**`
- `mypy/src/airflow_mypy/plugins/**`

## Context

- Module interfaces and utility contracts require standardized container specifications to prevent tight coupling to concrete data structure implementations.
- Multiple developer utility and plugin modules import and consume collections.abc abstractions to establish generic callable and sequence boundaries.
- Relying on concrete collections limits polymorphism and complicates argument handling across diverse utility subcommands and plugin hooks.

## Problem Statement

Coupling function signatures and component interfaces to concrete collection types restricts caller flexibility, complicates type-checking contracts, and causes brittle inter-module dependencies across tooling utilities and plugin extensions.

## Decision

- **R-LM-001** SHOULD: Components accepting ordered indexed collections SHOULD specify Sequence from collections.abc instead of concrete sequence types.

## Policy Block

In scope:

- Public and internal utility interfaces defining container or callable parameters.
- Plugin hooks, custom argument converters, and execution wrappers handling multi-element collections.

Out of scope:

- Internal algorithm implementations where specialized mutable container operations require concrete collection features.
- External protocol serialization boundaries requiring specific raw data payloads.

Exceptions:

- **EXC-20-001** Low-level performance-critical internal loops require direct concrete container method dispatch.
  Approval: Architecture review team
  Documentation: Document the performance benchmark results and rationale in the module documentation.

## Rationale

- Adopting collections.abc provides structural typing contracts that accept any conforming container without mandating inheritance.
- Evidence across multiple utility and plugin modules shows widespread adoption of Sequence, Callable, and Iterable to decouple callers from underlying data representations.
- Standardizing on protocol interfaces prevents accidental mutations and clarifies read-only versus mutable collection semantics.

## Consequences

Positive:

- Interface flexibility increases as callers can supply generators, tuples, lists, or custom collection wrappers without conversion overhead.
- Static verification and type analysis accurately detect container protocol mismatches without enforcing rigid concrete types.
- Component interfaces remain stable even when internal container implementations change.

Negative:

- Abstract protocols do not expose concrete container mutation methods, requiring explicit conversions when mutations are needed.
- Developers must understand structural subtyping distinctions between different collection protocols.

## Alternatives

- Coupling to concrete container types across all utility signatures (rejected)
  Rejected because: Concrete types force callers to convert data structures unnecessarily and limit interface interoperability.
  When valid: Only valid when in-place container mutation or specialized memory layout is an explicit interface requirement.
- Adopting custom internal protocol hierarchies for every container type (rejected)
  Rejected because: Reinventing standard container protocols introduces maintenance overhead and fragments the shared interface vocabulary.
  When valid: Only valid when specialized domain-specific container semantics cannot be expressed by standard collection protocols.

## Risks

- Consuming single-pass iterables where multiple iterations are expected by downstream logic.
  Mitigation: Enforce Sequence protocols instead of Iterable when multi-pass access or indexing is required.
  Owner: Engineering team
- Overly broad protocol annotations permitting unindexed collection access where sequence ordering is required.
  Mitigation: Specify Sequence or Mapping protocols explicitly during code review and automated linting checks.
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
- Inspect existing shared utility modules to discover prevailing container protocol conventions and type annotation practices.
- Verify that collection parameters intended for read-only consumption use abstract container protocols rather than concrete mutable types.

## Continuation Context

Verify commands:

- Discover the project verification script from the repository manifest and execute static analysis across utility modules.
- Run the repository type verification suite to validate conformance to abstract container protocols.

Accept when:

- Static type verification completes with zero type mismatch errors on collection signatures.
- All public utility interfaces consume abstract container protocols from collections.abc.

## Enforcement

- Verified by: Automated static type analysis in the continuous integration pipeline.
- Verified by: Peer review verification during pull request reviews.
- Violation handling: Pull requests introducing concrete container coupling in public interface signatures fail automated review checks.
- Violation handling: Developers must refactor non-conforming parameters to use appropriate abstract collection protocols.
- Exception process: Submit an exception request detailing specific container performance requirements to the architecture review team.
- Exception process: Document approved exceptions inline with reference to the approved exception tracking ticket.
