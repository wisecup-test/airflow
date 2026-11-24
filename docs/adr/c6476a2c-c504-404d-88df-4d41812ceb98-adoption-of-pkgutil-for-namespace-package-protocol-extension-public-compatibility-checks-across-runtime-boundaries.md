---
version: 2
name: c6476a2c-c504-404d-88df-4d41812ceb98-adoption-of-pkgutil-for-namespace-package-protocol-extension-public-compatibility-checks-across-runtime-boundaries
description: "Adoption of pkgutil for Namespace Package Protocol Extension: Public Compatibility Checks Across Runtime Boundaries"
metadata:
  actual:
    adr_id: c6476a2c-c504-404d-88df-4d41812ceb98
    status: accepted
    candidate_group_id: 65ab2eb6-88e1-4e66-8e46-d4c50096acab
    evidence:
      source: deep
  scope:
    root: providers/apache/druid
    paths:
      - src/airflow/**
---

# Adoption of pkgutil for Namespace Package Protocol Extension: Public Compatibility Checks Across Runtime Boundaries

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `providers/apache/druid`

Applies to:
- `src/airflow/**`

## Context

- The codebase distributes modular provider packages across separate directory structures while exposing them under a unified root namespace hierarchy.
- Namespace boundary packages require dynamic path resolution protocols to allow independently distributed modules to coexist within the same namespace tree.
- Static imports of concrete submodules within shared namespace initialization points introduce undesirable coupling and module resolution failures.

## Problem Statement

Distributing modular extensions across independent packages under a unified namespace requires a standardized protocol to stitch directory paths together at runtime without static dependencies or distribution conflicts.

## Decision

- **R-PEA-001** SHOULD: Public API compatibility checks across runtime boundaries SHOULD validate version contracts before exposing provider-specific interfaces.

## Policy Block

In scope:

- Package initialization modules defining shared root or intermediary provider namespaces.
- Modular provider distributions extending the common public namespace hierarchy.

Out of scope:

- Leaf implementation modules containing concrete domain logic or provider-specific operators.
- Self-contained packages that do not share namespace boundaries with other distributions.

## Rationale

- Dynamic protocol binding via pkgutil enables decoupled directory joining across separate distribution units.
- Adopting a uniform namespace protocol prevents package collision and ensures consistent module resolution behavior.
- Evidence confirms consistent usage of the dynamic import protocol across multiple levels of the provider namespace hierarchy.

## Consequences

Positive:

- Decouples provider distribution packages while maintaining a unified public import hierarchy.
- Permits independent distribution and modular installation of provider packages.
- Prevents static import side effects during namespace traversal.

Negative:

- Relies on runtime path manipulation which complicates static analysis tooling.
- Requires all distributed packages sharing the namespace to implement matching namespace protocols.

## Alternatives

- Monolithic single-package distribution without namespace splitting (rejected)
  Rejected because: Prevents modular independent distribution and forces unwanted dependencies on consumers.
  When valid: When all submodules are tightly coupled and released in lockstep under a single distribution unit.
- Implicit native namespace packages without initialization modules (rejected)
  Rejected because: Can create compatibility issues across legacy environments and inconsistent distribution packaging behavior.
  When valid: When all target runtimes and distribution packaging workflows exclusively support modern implicit namespace packages.

## Risks

- Inconsistent namespace protocol implementation across distributed packages causing import resolution failure.
  Mitigation: Enforce automated static analysis checks on all namespace boundary initialization modules.
  Owner: engineering team
- Accidental introduction of heavy imports into namespace boundary modules.
  Mitigation: Restrict namespace module contents to protocol extension directives and verify with import linting.
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
- Namespace initialization modules should contain minimal execution logic limited strictly to protocol-level namespace path extension.
- Downstream packages contributing to the shared namespace must adhere to identical namespace binding conventions to maintain package resolution integrity.

## Continuation Context

Verify commands:

- Discover the project test runner configuration and execute package import verification tests covering namespace resolution.
- Discover the project static analysis and linting suite to verify compliance with namespace initialization conventions.

Accept when:

- Namespace package modules successfully resolve and import across all distributed provider boundaries without path collisions.
- Dynamic package path extension passes project import validation suites across supported runtime environments.

## Enforcement

- Verified by: Automated continuous integration checks verifying clean imports of all provider packages.
- Verified by: Static analysis linting rules auditing namespace initialization modules.
- Violation handling: Pull requests introducing non-standard namespace initialization logic or heavy imports will be blocked by continuous integration.
- Violation handling: Violations detected in existing modules must be refactored to comply with the standard protocol.
- Exception process: Exceptions require review and approval by the architecture and release engineering maintainers.
- Exception process: Approved exceptions must be documented in the repository architectural exceptions log.
