---
version: 2
name: ec423acd-45b9-4a3c-85d2-c51e3bbf456b-public-provider-integration-contract-and-packaging-version-compatibility-parsing-version-normalization-exposed-via-dedicated-helper
description: "Public Provider Integration Contract and packaging.version Compatibility Parsing: Version Normalization Exposed Via Dedicated Helper"
metadata:
  actual:
    adr_id: ec423acd-45b9-4a3c-85d2-c51e3bbf456b
    status: accepted
    candidate_group_id: be490599-ed25-45b1-869a-0a8a3f622981
    evidence:
      source: deep
  scope:
    root: providers/apache/flink
    paths:
      - src/airflow/providers/apache/flink/**
---

# Public Provider Integration Contract and packaging.version Compatibility Parsing: Version Normalization Exposed Via Dedicated Helper

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `providers/apache/flink`

Applies to:
- `src/airflow/providers/apache/flink/**`

## Context

- Integration packages expose metadata and capability contracts to the primary orchestration framework.
- Host environment compatibility varies across deployments requiring programmatic version checks.
- Semantic version parsing across differing platform baselines requires standardized parsing conventions.

## Problem Statement

Integration packages must declare discovery metadata and manage compatibility across host platform versions without causing runtime import errors or contract discrepancies.

## Decision

- **R-PEA-001** SHOULD: Version normalization SHOULD be exposed via dedicated helper accessors such as get_base_airflow_version_tuple.

## Policy Block

In scope:

- Provider integration packages publishing public contract interfaces.
- Modules performing runtime environment version verification.

Out of scope:

- Internal utility routines not exposed outside the integration boundary.

## Rationale

- Contract definitions ensure that the host engine can discover provider packages programmatically.
- Utilizing packaging.version.Version normalizes version parsing across arbitrary runtime baselines, reducing regression risk.

## Consequences

Positive:

- Provides predictable capability discovery for host runtime integrations.
- Isolates runtime version differences behind standardized tuple accessors.
- Prevents runtime incompatibilities via robust semantic version comparisons.

Negative:

- Additional interface surface area must be maintained across core releases.
- Requires strict dependency management for semantic version parsing components.

## Alternatives

- Unstructured dictionary exports without formal provider contract interfaces (rejected)
  Rejected because: Fails contract validation and prevents automated external capability discovery.
  When valid: Internal single-module prototypes with no external consumption.
- String-based manual version parsing instead of packaging.version.Version parsing (rejected)
  Rejected because: Prone to parse errors across non-standard version specifiers and pre-release identifiers.
  When valid: Environments where external dependency packaging is strictly prohibited.

## Risks

- Provider metadata drifting from the host orchestrator expected specification.
  Mitigation: Define comprehensive unit tests against the public contract schema.
  Owner: engineering team
- Semantic version edge cases causing compatibility check failures.
  Mitigation: Use packaging.version parsing routines to guard feature enablement.
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
- Implement public contract functions as pure interfaces returning immutable metadata dictionaries.
- Centralize runtime version normalization into dedicated compatibility helper functions.

## Continuation Context

Verify commands:

- Discover and run the project contract verification suite to ensure provider metadata matches expected schemas.
- Discover and run the compatibility test suite across supported runtime matrix targets.

Accept when:

- All public contract entry points return schemas conforming to provider specifications.
- Runtime version compatibility checks successfully parse environment versions using packaging.version.Version semantics.

## Enforcement

- Verified by: Automated test suites validating contract schema adherence and version comparison matrix.
- Verified by: Pull request code review enforcing integration contract signatures.
- Violation handling: Build failure during automated continuous integration contract checks.
- Exception process: Exceptions require approval from framework core maintainers with a documented migration path.
