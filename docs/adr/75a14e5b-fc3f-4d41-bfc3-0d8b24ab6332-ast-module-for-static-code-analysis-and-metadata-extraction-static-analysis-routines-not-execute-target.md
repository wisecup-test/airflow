---
version: 2
name: 75a14e5b-fc3f-4d41-bfc3-0d8b24ab6332-ast-module-for-static-code-analysis-and-metadata-extraction-static-analysis-routines-not-execute-target
description: "ast Module for Static Code Analysis and Metadata Extraction: Static Analysis Routines Not Execute Target"
metadata:
  actual:
    adr_id: 75a14e5b-fc3f-4d41-bfc3-0d8b24ab6332
    status: accepted
    candidate_group_id: 6dd1586d-7267-4338-b301-9c70ca676031
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# ast Module for Static Code Analysis and Metadata Extraction: Static Analysis Routines Not Execute Target

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`
- `src/tests_common/test_utils/**`

## Context

- Documentation generation and test utilities require metadata about operators, hooks, sensors, and imports across diverse workspace components.
- Importing modules directly at documentation build time or test discovery time triggers module-level runtime execution, creates external service connections, and requires all optional provider dependencies to be installed.
- Static analysis via the ast module parses syntax trees and traverses node hierarchies to discover structural properties without executing module code or initiating network connections.
- Shared development utilities and documentation extensions standardize on ast.walk to inspect class definitions, call relationships, and decorator chains across the codebase.

## Problem Statement

Dynamic introspection of project modules during documentation builds and test discovery introduces heavy runtime dependencies and executes module initialization side effects. A mechanism is required to inspect class structures, decorators, and call hierarchies statically without importing target modules or executing their top-level code.

## Decision

- **R-LM-001** MUST_NOT: Static analysis routines MUST NOT execute target source files or import target modules into the active runtime process to obtain metadata.

## Policy Block

In scope:

- Documentation generation extensions that collect metadata about project classes, functions, and interfaces.
- Test utility modules that verify source file imports and structural contracts.
- Build-time and development-time source analysis utilities.

Out of scope:

- Runtime application execution and operational task scheduling.
- Modules executing within live worker or orchestration environments where full dependency resolution is already provided.

Exceptions:

- **EXC-001** A target module structural metadata cannot be determined statically due to dynamic metaprogramming or dynamic class creation.
  Approval: Architecture review board and core maintainers
  Documentation: Document the required runtime import and specify all required environment prerequisites in the affected module header.

## Rationale

- Utilizing ast.walk enables documentation extensions and test utilities to inspect module contracts without triggering side effects or initializing runtime configurations.
- Static analysis eliminates the requirement for developer and documentation environments to install every optional third-party library referenced by analyzed components.
- Parsing source syntax trees with the ast module provides consistent access to imports, function decorators, and method call maps across independent packages.

## Consequences

Positive:

- Eliminates runtime import side effects during documentation generation and test utility execution.
- Removes the need to install all external provider dependencies to inspect source structure and catalog classes.
- Standardizes AST traversal and metadata extraction protocols across shared developer tooling.

Negative:

- Static inspection requires explicit handling of AST node variants and cannot resolve dynamically constructed identifiers.
- Complex decorator patterns or dynamic metaprogramming require additional custom AST evaluation logic.

## Alternatives

- Runtime reflection using dynamic module imports and object inspection (rejected)
  Rejected because: Executing module-level imports requires all third-party dependencies to be installed and executes top-level side effects during documentation generation.
  When valid: When inspecting runtime state, active object instances, or dynamically injected attributes in a fully configured execution environment.
- Regular expression matching on raw source files (rejected)
  Rejected because: Regular expressions fail to parse multi-line definitions, nested decorators, complex class hierarchies, and context-dependent imports reliably.
  When valid: When performing single-line lexical scans or license header detection.

## Risks

- Language grammar updates between environment releases may introduce new AST node types that break traversal routines.
  Mitigation: Establish node evaluation fallback paths and verify AST parsing against the minimum supported language version declared in the project configuration.
  Owner: Engineering team
- Dynamic attribute assignment or aliased imports may escape static AST detection routines.
  Mitigation: Maintain comprehensive test suites covering import resolution mappings and structural call detection.
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
- Traverse parsed syntax trees using ast.walk and match against expected node structures to collect import references, method invocations, and class hierarchies.
- Use dedicated AST evaluation functions to safely resolve string literals and simple data structures without invoking unsafe runtime evaluation.

## Continuation Context

Verify commands:

- Discover the project test runner configuration from the repository manifest and execute the test suite covering documentation extensions and static analysis utilities.
- Discover and run the project code quality and linting verification scripts to confirm static analysis routines adhere to repository conventions.

Accept when:

- All test suites validating documentation extensions and test utilities complete with zero failures.
- Static source parsing extracts class metadata, decorators, and imports without raising unhandled syntax node exceptions or triggering runtime module imports.

## Enforcement

- Verified by: Continuous integration test suites validating static analysis utilities and documentation extensions.
- Verified by: Peer code review verifying that tooling and documentation components do not introduce dynamic runtime imports of target modules.
- Violation handling: Pull requests introducing direct runtime imports of target components in documentation extensions or test utilities will be blocked during review.
- Violation handling: Static analysis parsing failures or missing AST node handlers in automated builds will fail continuous integration validation.
- Exception process: Submit an architectural review request detailing why static AST inspection is insufficient for the targeted construct and obtain maintainer approval.
