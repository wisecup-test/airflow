---
version: 2
name: 97b2bd7f-b911-4339-b495-b3128930b40b-packaging-version-adoption-for-runtime-framework-version-compatibility-runtime-version-evaluation-expose-state-through
description: "packaging.version Adoption for Runtime Framework Version Compatibility: Runtime Version Evaluation Expose State Through"
metadata:
  actual:
    adr_id: 97b2bd7f-b911-4339-b495-b3128930b40b
    status: accepted
    candidate_group_id: 13463ee2-7989-4102-8c57-c8c0d3ea36c9
    evidence:
      source: deep
  scope:
    root: providers/apache/beam
    paths:
      - src/airflow/providers/apache/beam/**
---

# packaging.version Adoption for Runtime Framework Version Compatibility: Runtime Version Evaluation Expose State Through

Date: 2025-02-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `providers/apache/beam`

Applies to:
- `src/airflow/providers/apache/beam/**`

## Context

- Integration packages run alongside differing host framework installations and require runtime compatibility checks.
- Ad-hoc version string manipulation or custom splitting fails on complex release tags and standard-compliant specifiers.
- The codebase integrates packaging.version to normalize framework release strings through Version and expose standardized version contracts.

## Problem Statement

Integration components must adapt their behavior based on the runtime version of the host framework without failing when release strings include complex pre-release, post-release, or development identifiers. Without a standardized parsing specification, version comparisons become brittle and error-prone across differing deployment environments.

## Decision

- **R-LM-001** MUST: Runtime version evaluation MUST expose version state through standardized contract functions such as get_base_airflow_version_tuple rather than exposing unparsed strings directly to downstream integration code.

## Policy Block

In scope:

- Integration modules and provider packages that conditionally branch based on the runtime host framework version.
- Modules defining compatibility shims or feature-support contracts across host framework releases.

Out of scope:

- Core framework modules that do not interface across external release boundaries.
- Static dependency specifications resolved prior to runtime execution.

## Rationale

- Static analysis indicates adoption of packaging.version.Version to normalize framework release strings through get_base_airflow_version_tuple.
- Standardizing on packaging.version guarantees compliance with packaging standards for version parsing, avoiding edge cases in custom string manipulation.
- Encapsulating version parsing in a centralized compatibility helper prevents duplicated version logic and isolates external version parsing dependencies.

## Consequences

Positive:

- Deterministic and specification-compliant comparison of host framework release versions across runtime environments.
- Centralized compatibility contracts isolate provider code from changes in upstream version string formats.
- Eliminates defect surface caused by naive string parsing of complex release tags.

Negative:

- Introduces an explicit dependency on packaging.version for provider packages.
- Requires maintaining compatibility shims as host framework capabilities evolve across release cycles.

## Alternatives

- Ad-hoc string splitting and manual tuple comparison (rejected)
  Rejected because: Custom parsing fails on standard-compliant version suffixes such as pre-release and development tags.
  When valid: Valid only in environments where external dependencies are strictly prohibited and version strings strictly follow basic numeric notation.
- Feature detection via structural attribute inspection (rejected)
  Rejected because: Attribute presence does not reliably reflect behavioral changes or bugfix semantics across minor releases.
  When valid: Valid when checking for the existence of newly introduced modules rather than incremental behavioral updates.

## Risks

- Mismatched dependency resolution between host environment and provider package requirements.
  Mitigation: Verify resolved package version metadata in the repository lock artifact and test against minimum supported host versions.
  Owner: engineering team
- Uncached parsing overhead if Version constructor is invoked repeatedly in performance-sensitive code paths.
  Mitigation: Cache the computed version tuple in module-level scope so parsing occurs only once during import.
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
- Construct the packaging.version.Version object once at module initialization time and derive base version tuples to avoid redundant parsing during task execution.
- Expose helper functions returning integer tuples for comparative checks in downstream provider components.

## Continuation Context

Verify commands:

- Discover and run the test suite for compatibility modules using the project test runner.
- Execute the repository linter and static type checker across integration provider modules to verify API contract compliance.

Accept when:

- Version tuple generation accurately reflects runtime host framework releases across all configured test matrices.
- Static analysis checks pass with zero errors against declared version compatibility contracts.

## Enforcement

- Verified by: Automated continuous integration test matrices testing across supported host framework releases.
- Verified by: Static analysis and code review checking for unauthorized manual version string parsing.
- Violation handling: Automated continuous integration failure when version comparison rules are violated or unapproved string parsing is detected.
- Violation handling: Code review rejection for any pull request that bypasses centralized version compatibility helpers.
- Exception process: Submit an architectural deviation request detailing why standard version tuple contracts are insufficient for review by maintainers.
