---
version: 2
name: bff2428f-b91f-40d0-985e-150f81696153-adoption-of-devel-common-shared-library-module-project-adopt-devel-common-centralized-internal
description: "Adoption of devel-common Shared Library Module: Project Adopt Devel Common Centralized Internal"
metadata:
  actual:
    adr_id: bff2428f-b91f-40d0-985e-150f81696153
    status: accepted
    candidate_group_id: c6ea98b5-b85e-499a-ae7b-5efeae56426c
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/docs/utils/**
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Adoption of devel-common Shared Library Module: Project Adopt Devel Common Centralized Internal

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/docs/utils/**`
- `src/sphinx_exts/**`
- `src/tests_common/test_utils/**`

## Context

- Development, testing, and documentation build routines across multiple packages require common helper functions, assertion utilities, and build extensions.
- Decentralized or duplicate implementations of test harness utilities, AST parsers, and documentation transforms cause divergent testing behaviors and increased maintenance overhead.
- A dedicated internal workspace module devel-common is adopted to centralize shared test utilities, query tracing fixtures, logging assertions, and documentation build transforms.

## Problem Statement

Multiple packages within the repository require common testing assertions, query execution tracing, log verification utilities, and documentation generation extensions. Without a unified internal library module, individual packages duplicate test helpers and build transforms, leading to inconsistent test execution, duplicated logic, and brittle integration points.

## Decision

- **R-LM-001** MUST: The project MUST adopt devel-common as the centralized internal module for all shared development utilities, test execution helpers, query tracing fixtures, and documentation build extensions across repository packages.

## Policy Block

In scope:

- Shared test utilities, assertions, and query tracing helpers used across multiple test suites
- Documentation generation extensions, spelling verification routines, and build transforms
- First-party developer tooling and common file loading routines within the workspace

Out of scope:

- Production runtime business logic deployed to end users
- Package-specific unit test fixtures that have no applicability outside their host package
- Third-party upstream libraries maintained outside the workspace repository

## Rationale

- Evidence across multiple files demonstrates consistent consumption of devel-common for AST parsing, query execution tracing, log captures, and documentation transforms.
- Consolidating shared development infrastructure into a single internal module eliminates duplicate code, standardizes test assertions across packages, and ensures uniform documentation generation.
- Maintaining explicit contracts within devel-common decouples individual package test suites from lower-level environment setups while preserving modular workspace boundaries.

## Consequences

Positive:

- Centralizes common test utilities, query tracing, and documentation extensions in a single maintainable internal module.
- Ensures uniform behavior for test assertions, warning registry resets, and logging checks across all test suites.
- Eliminates duplicate helper functions across workspace packages, reducing codebase maintenance overhead.

Negative:

- Changes to shared contracts in devel-common can cascade across multiple dependent test suites and documentation builds.
- Requires disciplined module boundary management to prevent cyclic dependencies between shared utilities and package implementations.

## Alternatives

- Duplicate test utilities and build transforms locally within each individual package (rejected)
  Rejected because: Creates synchronization overhead, divergent testing behaviors, and widespread code duplication across the workspace.
  When valid: When packages are fully isolated independent repositories with no shared development lifecycle.
- Publish development utilities as an external third-party package on a public registry (rejected)
  Rejected because: Increases release ceremony and version latency for internal development tooling tightly coupled to the workspace repository.
  When valid: When the development utilities are stable, generic, and intended for public consumption by external organizations.

## Risks

- Breaking contract changes in devel-common can simultaneously break test suites and documentation pipelines across the workspace.
  Mitigation: Enforce automated workspace-wide regression testing for all pull requests modifying shared development utilities.
  Owner: engineering team
- Accidental coupling of shared development utilities to production runtime dependencies.
  Mitigation: Establish strict dependency boundaries ensuring development utilities remain confined to test and build scopes.
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
- Organize shared development code into modular subpackages separating test execution helpers from documentation build extensions.
- Export explicit public contracts from shared modules to prevent dependent packages from coupling to internal implementation details.

## Continuation Context

Verify commands:

- Discover the repository dependency manifest and execute the test suite for the shared development module.
- Execute the workspace-wide linter and static analysis checks to verify no circular dependencies exist between packages and the shared module.

Accept when:

- All unit and integration tests within the shared development module pass successfully without errors.
- Dependent package test suites and documentation build pipelines successfully import and execute shared utilities without failures.

## Enforcement

- Verified by: Continuous integration pipelines executing workspace test suites across all packages
- Verified by: Static analysis checks detecting duplicated development utilities or unauthorized circular imports
- Verified by: Peer review on pull requests modifying shared development module contracts
- Violation handling: Pull requests introducing duplicated test helpers or bypassing the shared development module are blocked during code review.
- Violation handling: Continuous integration failures resulting from incompatible changes in shared contracts block merging until resolved.
- Exception process: Package authors requiring specialized test utilities not suitable for sharing must document the rationale in the package test directory.
- Exception process: Exceptions must be reviewed and approved by workspace architecture maintainers.
