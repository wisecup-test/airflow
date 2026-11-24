---
version: 2
name: b60d3a70-a7da-47f0-9b6c-8ec36952a2d6-adoption-of-pathlib-for-object-oriented-filesystem-path-operations-convert-path-instances-posix-compliant-string
description: "Adoption of pathlib for Object-Oriented Filesystem Path Operations: Convert Path Instances Posix Compliant String"
metadata:
  actual:
    adr_id: b60d3a70-a7da-47f0-9b6c-8ec36952a2d6
    status: accepted
    candidate_group_id: 856787ff-8f11-4941-bc74-ebebdb89c5af
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/docs/utils/**
      - src/sphinx_exts/**
      - src/tests_common/**
---

# Adoption of pathlib for Object-Oriented Filesystem Path Operations: Convert Path Instances Posix Compliant String

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/docs/utils/**`
- `src/sphinx_exts/**`
- `src/tests_common/**`

## Context

- Filesystem interactions across developer tooling, test utilities, and build extensions require consistent path resolution across diverse deployment environments.
- Procedural string-based path manipulation creates maintenance friction through nested function calls and platform separator discrepancies.
- Multiple internal modules across build and test infrastructure have converged on object-oriented path protocols for location anchoring, directory traversal, and file reading.

## Problem Statement

Inconsistent filesystem path manipulation across developer tooling and build extensions introduces subtle platform-dependent discrepancies, type ambiguities, and verbose procedural code. The lack of a uniform path representation complicates resource location referencing, directory hierarchy navigation, and file content inspection.

## Decision

- **R-LM-001** MAY: Convert Path instances to POSIX-compliant string representations via Path.as_posix when interfacing with external boundary systems requiring string inputs.

## Policy Block

In scope:

- Modules providing developer tooling, build orchestration, documentation generation, search indexing, and test utilities.
- Internal filesystem path construction, hierarchy navigation, directory search, and file content reading.

Out of scope:

- Direct invocations of external third-party library APIs that strictly require primitive string path types and lack object-oriented path interoperability.
- Performance-critical low-level operating system bindings where object allocation overhead is demonstrably prohibitive.

Exceptions:

- **EXC-20-001** Interfacing with external third-party library boundaries that strictly enforce primitive string type validation on file arguments.
  Approval: Architecture review team
  Documentation: Explicit inline comments documenting the boundary requirement and immediate conversion from Path objects.

## Rationale

- Evidence across fifteen independent infrastructure modules demonstrates widespread convergence on pathlib for path anchoring, file inspection, and hierarchy traversal.
- Object-oriented path abstractions provide cohesive method chaining, explicit type contracts, and built-in filesystem inspection methods that eliminate procedural nesting.
- Standardizing on a unified path object contract minimizes impedance mismatches across internal modules and ensures consistent cross-platform behavior across environments.

## Consequences

Positive:

- Provides composable method chaining for filesystem hierarchy navigation, relative resolution, and pattern matching.
- Eliminates platform-specific path separator incompatibilities across different host execution environments.
- Strengthens type contracts across internal module boundaries by replacing ambiguous string types with explicit Path representations.
- Simplifies filesystem inspection and content reading through unified instance methods.

Negative:

- Requires explicit string conversion when interfacing with external library boundaries that mandate primitive string path types.
- Introduces transitional maintenance overhead where legacy procedural path calls coexist alongside object-oriented path abstractions.

## Alternatives

- Procedural string-based path manipulation using legacy operating system modules (rejected)
  Rejected because: String concatenation and procedural path routines lack unified typing, introduce platform separator inconsistencies, and require verbose nested function calls for basic navigation.
  When valid: Runtimes lacking native object-oriented path library support.
- Proprietary internal path abstraction wrapper class (rejected)
  Rejected because: Creating custom path wrappers increases maintenance overhead and reinvents standard object-oriented filesystem paradigms already provided by standard library modules.
  When valid: Architectures requiring specialized virtual filesystem abstractions or custom remote storage layers.

## Risks

- Partial migration leaving hybrid usage where string paths and Path objects are intermingled, leading to runtime type errors at unannotated boundaries.
  Mitigation: Enforce strict type annotations using Path types and automated static analysis checks during integration review.
  Owner: Engineering team
- Inconsistent handling of path representations when passing paths to external libraries expecting primitive strings.
  Mitigation: Explicitly invoke Path conversion methods at the outermost integration boundary.
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
- Anchor module-relative filesystem references to current file tokens through Path constructors and navigate directory structures using operator composition and parent attributes.
- Isolate string conversions to the boundary layer when interfacing with external third-party functions that do not accept Path instances directly.

## Continuation Context

Verify commands:

- Discover and run the project static analysis suite to verify that path manipulation complies with the object-oriented path interface standard.
- Discover and run the project type checking suite to ensure all path arguments adhere to Path type annotations.
- Discover and run the project test execution suite to validate that filesystem navigation and relative path resolutions succeed across target environments.

Accept when:

- All path manipulations across developer tooling and build modules use Path objects for navigation, resolution, and inspection.
- Interface contracts across internal modules annotate path parameters and return values with Path type structures.
- Automated static analysis and type verification directives pass without detecting procedural path concatenations in targeted modules.

## Enforcement

- Verified by: Automated static analysis and linting checks executed during continuous integration workflows.
- Verified by: Static type verification suites checking path parameter annotations across internal modules.
- Verified by: Peer review validation on all code modifications touching developer and build tooling infrastructure.
- Violation handling: Continuous integration verification runs fail upon detecting procedural path manipulation routines in targeted modules.
- Violation handling: Pull requests containing unencapsulated string path manipulations are blocked until refactored to conform with object-oriented path standards.
- Exception process: Submit an architecture exception request detailing third-party boundary compatibility constraints that strictly mandate string primitives.
- Exception process: Document approved exceptions with explanatory inline comments and isolate primitive conversions at the outermost integration boundary.
