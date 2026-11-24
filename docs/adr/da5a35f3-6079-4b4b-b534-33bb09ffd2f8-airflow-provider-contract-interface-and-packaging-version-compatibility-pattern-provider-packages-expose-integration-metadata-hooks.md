---
version: 2
name: da5a35f3-6079-4b4b-b534-33bb09ffd2f8-airflow-provider-contract-interface-and-packaging-version-compatibility-pattern-provider-packages-expose-integration-metadata-hooks
description: "Airflow Provider Contract Interface and Packaging Version Compatibility Pattern: Provider Packages Expose Integration Metadata Hooks"
metadata:
  actual:
    adr_id: da5a35f3-6079-4b4b-b534-33bb09ffd2f8
    status: accepted
    candidate_group_id: 595312d9-672a-4216-825d-1b639f88b2e3
    evidence:
      source: deep
  scope:
    root: providers/apache/beam
    paths:
      - src/airflow/providers/apache/beam/**
---

# Airflow Provider Contract Interface and Packaging Version Compatibility Pattern: Provider Packages Expose Integration Metadata Hooks

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `providers/apache/beam`

Applies to:
- `src/airflow/providers/apache/beam/**`

## Context

- Provider packages need a standard mechanism to advertise metadata, integrations, and capabilities to the host orchestration platform.
- Provider packages often need to run across multiple platform versions that differ in internal API contracts and capabilities.
- Adopting standardized contract discovery functions and robust semantic version comparison eliminates brittle ad-hoc version checking.

## Problem Statement

Provider integrations must expose structured discovery metadata to the host orchestration framework while supporting heterogeneous platform runtime versions without runtime contract breakage or brittle version string comparisons.

## Decision

- **R-PEA-001** MUST: Provider packages MUST expose integration metadata, hooks, and capabilities through the canonical get_provider_info public contract function.

## Policy Block

In scope:

- Provider integration packages that expose public contracts and capabilities to the host orchestration platform.
- Provider modules performing runtime platform version negotiation and compatibility checks.

Out of scope:

- Internal private modules within provider packages that do not participate in platform discovery or version gating.
- Standalone utilities and test helpers operating outside the platform provider contract ecosystem.

## Rationale

- Using a standardized public contract function allows the host platform to discover capabilities, hooks, and operators dynamically without coupling to internal module paths.
- Employing structured Version objects from the packaging library avoids parsing errors inherent in raw string or naive tuple comparisons across complex release identifiers.
- Centralizing platform version detection into dedicated compatibility helpers isolates API drift and simplifies ongoing provider maintenance.

## Consequences

Positive:

- Establishes a uniform, discoverable integration interface between independent provider packages and the core platform runtime.
- Ensures safe, semantically precise version negotiation across varying core platform releases.
- Prevents runtime attribute errors and import failures when platform features evolve across releases.

Negative:

- Maintaining compatibility abstractions and version checks increases maintenance overhead across provider release cycles.
- Requires an explicit dependency on semantic version parsing utilities within the provider package runtime.
- Contract mismatches between provider metadata schemas and platform discovery engines can delay integration readiness.

## Alternatives

- Direct platform version string comparison or custom tuple slicing (rejected)
  Rejected because: String and basic tuple comparisons fail to handle platform release candidate tags, post-releases, and complex semantic version specifications accurately.
  When valid: Only viable in zero-dependency script environments where semantic version parsing utilities are unavailable.
- Static manifest metadata declarations without runtime discovery contracts (rejected)
  Rejected because: Static configuration files cannot dynamically evaluate environment capabilities or adapt version compatibility logic during runtime discovery.
  When valid: Applicable to purely declarative packages that require no runtime environment negotiation.

## Risks

- Provider contract schema changes in newer host platforms could lead to invalid metadata responses.
  Mitigation: Enforce automated schema validation on provider contract dictionaries during continuous integration testing.
  Owner: engineering team
- Accumulation of complex version branches can degrade code maintainability and test matrix complexity.
  Mitigation: Maintain centralized compatibility wrappers with comprehensive test coverage covering supported platform releases.
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
- Encapsulate cross-release capability branches in centralized compatibility helper routines rather than scattering version guards across operator implementations.
- Ensure that provider information contract functions return dictionary structures complying with the platform discovery schema.

## Continuation Context

Verify commands:

- Discover and execute the repository test suite to verify provider contract interfaces and version compatibility routines against all supported environments.
- Discover and execute the metadata validation scripts to confirm that the provider information dictionary satisfies host platform contract expectations.

Accept when:

- All provider contract entry points expose compliant metadata structures matching core orchestrator integration requirements.
- Version comparison routines consistently use structured Version objects from the packaging library without runtime type exceptions.
- The project test suite and contract validation scripts pass without compatibility regressions across supported runtime configurations.

## Enforcement

- Verified by: Automated contract test suites validating provider discovery metadata return structures.
- Verified by: Continuous integration matrix builds running provider suites across supported core platform environments.
- Verified by: Pull request architecture reviews inspecting public interface implementations and version compatibility logic.
- Violation handling: Pull requests missing the required provider discovery contract or employing non-standard version parsing must fail automated verification.
- Violation handling: Non-compliant version checks must be flagged during static inspection and blocked prior to integration release.
- Exception process: Proposals to introduce alternative contract hooks or versioning mechanisms require review and approval from platform architecture maintainers.
- Exception process: Any temporary exception must be documented in the provider implementation with a tracking ticket and deprecation timeline.
