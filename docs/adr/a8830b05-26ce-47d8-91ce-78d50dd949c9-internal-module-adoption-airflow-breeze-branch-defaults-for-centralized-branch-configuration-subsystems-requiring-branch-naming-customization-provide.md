---
version: 2
name: a8830b05-26ce-47d8-91ce-78d50dd949c9-internal-module-adoption-airflow-breeze-branch-defaults-for-centralized-branch-configuration-subsystems-requiring-branch-naming-customization-provide
description: "Internal Module Adoption: airflow_breeze.branch_defaults for Centralized Branch Configuration: Subsystems Requiring Branch Naming Customization Provide"
metadata:
  actual:
    adr_id: a8830b05-26ce-47d8-91ce-78d50dd949c9
    status: accepted
    candidate_group_id: 28cd176b-f26a-4e67-b7a4-9c27afd64a54
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Internal Module Adoption: airflow_breeze.branch_defaults for Centralized Branch Configuration: Subsystems Requiring Branch Naming Customization Provide

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`

## Context

- Parameter models and command option parsers across the system require baseline branch references and constraints branch identifiers to construct operational configurations.
- Hardcoding string literals across multiple separate modules creates maintenance divergence and inconsistent default behaviors.
- The codebase establishes an internal shared module, airflow_breeze.branch_defaults, to centralize default branch references.
- Static analysis confirms repeated imports and references to airflow_breeze.branch_defaults across parameter objects and command option definitions.

## Problem Statement

Scattering default branch names and constraints references across multiple parameter classes and command option definitions risks configuration drift, inconsistent defaults across environments, and high maintenance overhead when branch naming conventions change.

## Decision

- **R-LM-001** MAY: Subsystems requiring branch naming customization MAY provide override mechanisms that fall back to constants exported by airflow_breeze.branch_defaults.

## Policy Block

In scope:

- All command option definitions and parameter classes requiring baseline branch references or constraints references.

Out of scope:

- External integration tests and standalone scripts operating outside the main command execution lifecycle.

## Rationale

- Centralizing branch defaults in airflow_breeze.branch_defaults provides a single authoritative source of truth across all commands and parameter classes.
- Decoupling branch configuration from individual command option modules prevents accidental parameter divergence across distinct build profiles.
- Static code analysis confirms consistent usage of shared branch defaults across both CI and production build parameter architectures.

## Consequences

Positive:

- Ensures consistency of branch naming and constraints references across all execution targets.
- Simplifies future updates to branch defaults by isolating modifications to a single module.
- Reduces redundant string literals and eliminates configuration drift in parameter construction.

Negative:

- Creates an internal dependency on airflow_breeze.branch_defaults across option definitions and parameter models.
- Requires developers to update centralized constants rather than defining ad-hoc local defaults.

## Alternatives

- Localized hardcoded string literals across each parameter and command option module (rejected)
  Rejected because: Causes parameter divergence, inconsistent default branches across workflows, and elevated maintenance burden when updating branch naming conventions.
  When valid: Isolated single-file exploratory prototypes that do not integrate into the broader configuration lifecycle.
- Externalized environment variable lookups without module-level constant fallbacks (rejected)
  Rejected because: Increases operational fragility by requiring environment variables to be explicitly defined for every execution environment.
  When valid: Fully dynamic execution environments where no baseline project defaults exist.

## Risks

- Modifications to branch constants in airflow_breeze.branch_defaults could unintentionally impact multiple downstream commands.
  Mitigation: Validate all dependent parameter structures through automated suite execution prior to merging updates to the defaults module.
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
- Import branch constants directly from airflow_breeze.branch_defaults when initializing default values for parameters or option flags.
- When introducing new build parameter profiles, reference existing constant structures rather than creating standalone branch strings.

## Continuation Context

Verify commands:

- "${PROJECT_LINTER} $(find . -type f -exec grep -l 'airflow_breeze.branch_defaults' {} +)"
- "${PROJECT_TEST_RUNNER} ${PROJECT_TEST_FLAGS}"

Accept when:

- All parameter definitions and command options reference airflow_breeze.branch_defaults without localized literal duplication.
- The discovered verification test suite passes without assertion or configuration failures.

## Enforcement

- Verified by: Automated static analysis checks in the continuous integration pipeline.
- Verified by: Peer review verification on code contributions modifying parameters or command options.
- Violation handling: Contributions containing localized hardcoded branch literals will fail continuous integration checks and require refactoring to use the shared module.
- Exception process: Exceptions require documented approval from the architecture team detailing why centralized module constants cannot be utilized.
