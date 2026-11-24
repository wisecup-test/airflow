---
version: 2
name: 8b8d6f60-15c7-4ec7-82e5-bc04828ac236-centralized-domain-constants-module-adoption-with-airflow-breeze-global-constants-command-option-builders-parameter-parsing-structures
description: "Centralized Domain Constants Module Adoption with airflow_breeze.global_constants: Command Option Builders Parameter Parsing Structures"
metadata:
  actual:
    adr_id: 8b8d6f60-15c7-4ec7-82e5-bc04828ac236
    status: accepted
    candidate_group_id: ddff902e-4340-4aec-a74e-404896994ed8
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Centralized Domain Constants Module Adoption with airflow_breeze.global_constants: Command Option Builders Parameter Parsing Structures

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`

## Context

- Tooling subpackages coordinate interface parameter choices, container build arguments, artifact caching paths, and verification routines across disparate modules.
- Dispersing shared constants such as allowed runtime versions, backend platforms, and cache directory locations across individual command modules causes cross-module drift and silent synchronization failures.
- A unified internal constants module provides authoritative, single-source-of-truth collections and defaults that consuming interfaces must reference directly.

## Problem Statement

Multiple functional modules within developer tooling require access to identical domain constants, including permitted runtime versions, supported infrastructure backends, parameter choices, and build artifact cache paths. Duplicating these constant values across disparate command declarations and utility scripts introduces maintenance overhead, elevated risk of synchronization divergence, and inconsistent validation across execution boundaries.

## Decision

- **R-LM-001** SHOULD: Command option builders and parameter parsing structures SHOULD wrap collection constants imported from airflow_breeze.global_constants inside dedicated choice validation wrappers to guarantee user input matches authorized values.

## Policy Block

In scope:

- All developer tooling commands, parameter mapping classes, and utility modules within the tooling package that reference shared choice enumerations, allowed platform versions, or build cache paths.

Out of scope:

- Module-internal execution state, isolated temporary variable definitions, and component-specific private flags that have no external architectural consumers.

Exceptions:

- **EXC-20-001** A specialized testing fixture requires ephemeral mocked constant sets that simulate unsupported configuration permutations.
  Approval: Tooling architecture maintainers
  Documentation: Documented inline within the isolated test fixture specifying the justification for deviating from the central module.

## Rationale

- Centralizing permitted runtime parameters and build paths in airflow_breeze.global_constants ensures uniform validation behavior across both interactive command invocation and unattended automation pipelines.
- Evidence across nine disparate operational modules establishes that option definitions, execution checkers, and parameter classes consistently depend on a single shared constants namespace.
- Extracting domain constants from execution routines establishes strict boundaries between immutable architectural choices and dynamic command orchestrations.

## Consequences

Positive:

- Eliminates configuration divergence by maintaining one canonical location for permitted options, supported platforms, and default paths.
- Simplifies future updates to supported runtime matrices and infrastructure choices by localizing changes to a single source file.
- Provides consistent user-facing error reporting and validation across all CLI commands through shared enumeration bindings.

Negative:

- Creates a centralized dependency node where frequent modifications to supported constants may trigger broad re-execution of test suites.
- Requires consumers to update shared constants even when introducing provisional or experimental options intended for limited scope.

## Alternatives

- Decentralized local constant definitions maintained individually inside each command and utility module (rejected)
  Rejected because: Causes option sets to diverge over time, leading to silent mismatches between command line choices and execution backend logic.
  When valid: When an option is completely unique to a single operational routine and will never be shared or validated across boundaries.
- Dynamic configuration discovery loaded from external structured markup files at runtime (rejected)
  Rejected because: Introduces runtime file resolution latency, additional parsing failure modes, and loss of static analysis type inference.
  When valid: When configuration values must be modified dynamically without code deployments.

## Risks

- Accidental in-place mutation of mutable data structures exported by the shared constants module by downstream consumers.
  Mitigation: Export immutable tuple or frozen data structures, or enforce copy semantics when consuming constants.
  Owner: engineering team
- Merge conflicts arising from multiple concurrent modifications to the centralized constants file.
  Mitigation: Organize constant definitions into logical grouped sections within the module and enforce discrete PR reviews.
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
- When defining new allowed choices or system paths, add the canonical definition to airflow_breeze.global_constants and import that symbol in both the command option specification and the underlying execution parameter model.
- Verify that all consuming modules import constants directly by symbol name rather than performing wildcard imports or re-exporting constants across intermediate layers.

## Continuation Context

Verify commands:

- test -n "$(find . -type f -name "*.py" -exec grep -l "airflow_breeze.global_constants" {} +)" && echo "Central constants references verified"
- # Discover and execute repository test suite for developer tooling
- # Discover and execute repository linting suite for import enforcement

Accept when:

- All references to shared configuration choices and build constants import directly from airflow_breeze.global_constants.
- No duplicate local literal definitions of shared constant sets exist across tooling command and parameter modules.
- The discovered project test and linting verification suites execute successfully with zero violations.

## Enforcement

- Verified by: Automated continuous integration checks executing import linting and static type analysis.
- Verified by: Peer code review verification on pull requests modifying developer tooling options or parameters.
- Violation handling: Pull requests introducing local duplicate constant sets or bypassing the centralized constants module will fail continuous integration validation.
- Violation handling: Code review rejections requiring contributors to consolidate duplicated values into airflow_breeze.global_constants before merge.
- Exception process: Submit an exception request to the tooling architecture maintainers documenting the necessity for local scoping or experimental divergence.
- Exception process: Upon approval, document the exception with inline justification as specified in the policy exception block.
