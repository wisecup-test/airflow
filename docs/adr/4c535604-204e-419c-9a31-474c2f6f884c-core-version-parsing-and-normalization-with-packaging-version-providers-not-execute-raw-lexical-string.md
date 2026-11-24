---
version: 2
name: 4c535604-204e-419c-9a31-474c2f6f884c-core-version-parsing-and-normalization-with-packaging-version-providers-not-execute-raw-lexical-string
description: "Core Version Parsing and Normalization with packaging.version: Providers Not Execute Raw Lexical String"
metadata:
  actual:
    adr_id: 4c535604-204e-419c-9a31-474c2f6f884c
    status: accepted
    candidate_group_id: c70d7b7e-886e-44b2-bf1c-2bf49a18609f
    evidence:
      source: deep
  scope:
    root: providers/apache/flink
    paths:
      - src/airflow/providers/apache/flink/**
---

# Core Version Parsing and Normalization with packaging.version: Providers Not Execute Raw Lexical String

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `providers/apache/flink`

Applies to:
- `src/airflow/providers/apache/flink/**`

## Context

- Host framework and provider package lifecycles evolve asynchronously, requiring downstream provider modules to conditionally adapt to available runtime capabilities.
- Version identifiers in the host ecosystem frequently incorporate release candidates, post-releases, or metadata that break simple string-splitting comparisons.
- Compatibility logic in provider modules relies on packaging.version to provide robust, specification-compliant parsing of host version identifiers.

## Problem Statement

Provider modules that interface with core runtime facilities must verify host environment capabilities across heterogeneous deployment targets. Performing manual or lexical string comparisons against host version strings introduces defect risk when encountering pre-releases or multi-digit minor versions, leading to broken compatibility branching and runtime failures.

## Decision

- **R-LM-001** MUST_NOT: Providers MUST_NOT execute raw lexical string comparisons or ad-hoc regular expression matching on host version attributes to determine capability availability.

## Policy Block

In scope:

- Provider modules and extension packages that perform runtime feature detection against the core host framework.

Out of scope:

- Internal core framework modules that do not maintain cross-version backwards compatibility contracts.

## Rationale

- Utilizing packaging.version.Version standardizes semantic version evaluation according to established interoperability standards.
- Encapsulating version resolution behind get_base_airflow_version_tuple prevents repetitive parsing logic and ensures consistent version comparison across integration boundaries.

## Consequences

Positive:

- Deterministic version comparison prevents defects caused by alphanumeric suffix evaluation and multi-digit minor releases.
- Centralized compatibility contracts isolate version-parsing dependencies to dedicated compatibility adapters.

Negative:

- Introduces a runtime coupling to packaging within provider compatibility layers.
- Requires maintaining dedicated version compatibility adapters alongside provider feature code.

## Alternatives

- Lexical string comparison of version identifiers (rejected)
  Rejected because: Lexical comparisons fail when minor or patch numbers exceed single digits and cannot reliably handle pre-release or build tags.
  When valid: Only valid in trivial single-digit static version environments.
- Ad-hoc string splitting and manual integer casting (rejected)
  Rejected because: Manual splitting yields unpredictable exceptions when version strings contain suffixes, prefixes, or development identifiers.
  When valid: Valid when external dependencies cannot be introduced and version strings strictly adhere to dot-separated integers.

## Risks

- Changes in upstream host version formatting or packaging.version parsing behavior could affect tuple derivation.
  Mitigation: Cover version normalization with automated unit tests spanning diverse version formats including release candidates.
  Owner: engineering team
- Inconsistent version helper implementations across multiple independent provider packages.
  Mitigation: Standardize on a single compatibility contract pattern across all provider boundary modules.
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
- Define compatibility contracts as dedicated helper functions returning comparable numeric tuples rather than leaking third-party parsing objects across caller boundaries.
- Locate the installed runtime version attribute at module initialization and extract the primary tuple components once to minimize execution overhead.

## Continuation Context

Verify commands:

- Discover the project test runner script in the repository configuration and execute the unit test suite targeting compatibility modules.
- Inspect dependency manifests and repository lock files to confirm proper declaration of packaging primitives.

Accept when:

- Unit tests verify that get_base_airflow_version_tuple produces correct numeric tuples for standard, pre-release, and development version strings.
- Static analysis confirms that no raw lexical version string comparisons exist within provider integration modules.

## Enforcement

- Verified by: Automated test suites executed during continuous integration workflows.
- Verified by: Peer code review for all new and modified provider compatibility code.
- Violation handling: Pull requests containing raw string comparisons for version checking will be blocked until refactored to use the standardized compatibility contract.
- Exception process: Exceptions require review and approval by integration architects when interacting with upstream systems that do not follow standard semantic versioning.
