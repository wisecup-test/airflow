---
version: 2
name: 899f092c-51aa-4fe3-959c-f00d8cc57f45-adoption-of-collections-abc-for-container-and-callable-interface-protocols-components-defining-public-interfaces-data-renderers
description: "Adoption of collections.abc for Container and Callable Interface Protocols: Components Defining Public Interfaces Data Renderers"
metadata:
  actual:
    adr_id: 899f092c-51aa-4fe3-959c-f00d8cc57f45
    status: accepted
    candidate_group_id: 52ea4356-a278-4a3f-83da-2c5b4a6792a7
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# Adoption of collections.abc for Container and Callable Interface Protocols: Components Defining Public Interfaces Data Renderers

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `src/airflowctl/**`

## Context

- CLI argument parsing, console output rendering, and data transformation routines exchange structured data and transformation functions across module boundaries.
- Direct reliance on concrete container types restricts caller inputs and forces unnecessary data conversions prior to formatting or parsing.
- The codebase uses collections.abc protocols across utility helpers, console formatters, and CLI parser modules to specify abstract parameter and return contracts.

## Problem Statement

Direct coupling to concrete container implementations across CLI parsing and rendering utilities restricts caller flexibility, forces redundant data conversions, and obscures structural contracts across internal module boundaries. Without standardized abstract interface protocols, utility functions cannot reliably accept polymorphic sequences, streams, or callable mappers.

## Decision

- **R-LM-001** MUST: Components defining public interfaces, data renderers, or collection transformation utilities MUST declare parameter contracts and return contracts using abstract protocols from collections.abc rather than binding to concrete container classes.

## Policy Block

In scope:

- Public interface contracts, helper utilities, and formatting routines that accept or return collection types or callable handlers.
- Cross-module boundaries connecting CLI argument parsers to console formatters and execution commands.

Out of scope:

- Private function scopes where collection instantiation and mutation are entirely internal to a single execution block.
- External transport serialization boundaries requiring concrete schema instance bindings.

## Rationale

- Binding interfaces to abstract protocols from collections.abc decouples rendering and transformation logic from concrete collection implementations, allowing data sequences, generators, and custom collections to be handled interchangeably.
- Specifying Callable and Sequence contracts clarifies caller expectations and prevents brittle reliance on concrete list or dictionary data structures across CLI boundary operations.
- Evidence across formatting utilities, CLI parsers, and helper modules demonstrates consistent reuse of collections.abc abstractions to standardize data normalization and pipeline dispatching.

## Consequences

Positive:

- Decouples CLI argument parsers, terminal renderers, and helper utilities from concrete container types.
- Enables polymorphic inputs such as sequences, generators, and custom callable mappers without redundant conversions.
- Improves contract precision and static verification across module boundaries.

Negative:

- Requires developers to distinguish between abstract container protocols and concrete collection capabilities during API design.
- Requires explicit data normalization when downstream presentation formatters require tabular or dictionary structures.

## Alternatives

- Constrain all utility and interface signatures to concrete container classes (rejected)
  Rejected because: Couples parsers and formatters to specific data structures, forcing unnecessary intermediate memory allocations and collection conversions across module boundaries
  When valid: When operations strictly require in-place mutation methods unique to a specific concrete container class
- Rely on unannotated duck typing without formal collection interface protocols (rejected)
  Rejected because: Obscures parameter expectations, eliminates static contract enforcement, and increases runtime contract defects at subsystem boundaries
  When valid: In small, isolated private scripts where formal interface validation is not maintained

## Risks

- Callers passing single-pass iterables to multi-pass formatting algorithms expecting reusable sequences
  Mitigation: Enforce Sequence interface contracts in signatures and validate sequence indexing or length requirements at boundary entry points.
  Owner: Engineering team
- Behavioral discrepancies across differing runtime environments when resolving protocol definitions
  Mitigation: Verify environment lock artifacts and execute automated static type verification across all interface boundaries.
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
- When implementing data presentation or transformation routines, declare collections.abc.Sequence for indexable input sequences and collections.abc.Callable for custom mapping functions.
- Use normalization routines to validate and transform heterogeneous sequence elements into uniform dictionary structures prior to invoking specialized table or structured output renderers.

## Continuation Context

Verify commands:

- Discover and execute the repository static analysis and type checking suite to verify protocol compliance.
- Discover and execute the repository test suite to confirm polymorphic collection handling across formatters and parsers.

Accept when:

- All collection-consuming public interfaces declare collections.abc protocols and pass static contract verification without type violations.
- Formatting utilities and command parsing routines pass all automated test suites when handling conforming sequence and callable inputs.

## Enforcement

- Verified by: Automated static analysis and type verification executed in continuous integration pipelines.
- Verified by: Peer code reviews for all cross-module contract changes and public function signatures.
- Violation handling: Code review approvals are withheld and pull requests are blocked if new interface boundaries specify concrete container types where collections.abc protocols suffice.
- Exception process: Exceptions require documented justification during code review demonstrating why an abstract collection protocol cannot satisfy the mutation or operational requirement.
