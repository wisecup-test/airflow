---
version: 2
name: d77eb761-747b-426a-8dab-deed4f7f044d-environment-variable-resolution-via-standard-os-runtime-interface-modules-not-perform-unchecked-dictionary-indexing
description: "Environment Variable Resolution via Standard os Runtime Interface: Modules Not Perform Unchecked Dictionary Indexing"
metadata:
  actual:
    adr_id: d77eb761-747b-426a-8dab-deed4f7f044d
    status: accepted
    candidate_group_id: ef443560-9d9d-4269-bf59-740c85651b0f
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - docs/**
      - hatch_build.py
---

# Environment Variable Resolution via Standard os Runtime Interface: Modules Not Perform Unchecked Dictionary Indexing

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `docs/**`
- `hatch_build.py`

## Context

- Runtime configuration values and operational flags are supplied via host process environment variables across build hooks and documentation generation modules.
- Components inspect environment variables using direct standard runtime library calls rather than routing through a dedicated external configuration framework.
- Observed implementations show split retrieval semantics between direct dictionary key indexing and optional retrieval with fallback default values.

## Problem Statement

Direct access to environment variables without a shared configuration abstraction or schema validation introduces inconsistent fallback behaviors and failure modes across execution contexts.

## Decision

- **R-CEM-001** SHOULD_NOT: Modules SHOULD_NOT perform unchecked dictionary indexing on environment collections where a missing variable would terminate execution unexpectedly, unless immediate termination is the intended failure behavior.

## Policy Block

In scope:

- Build preparation routines and documentation generation modules accessing host environment variables.
- Scripts executing in containerized or continuous integration environments requiring configuration parameterization.

Out of scope:

- Core runtime application components governed by formal centralized settings schemas or validation frameworks.
- Internal service communication payloads and inter-process messaging data.

Exceptions:

- **EXC-37-001** A missing environment variable indicates an unrecoverable configuration state where immediate termination is required.
  Approval: Architecture Review Board
  Documentation: Document the mandatory environment variable in the project setup guidelines.

## Rationale

- Direct environment variable access avoids introducing heavy third-party configuration dependencies into lightweight build hooks and auxiliary documentation scripts.
- Providing fallback default values ensures reproducible execution in local environments where non-critical environment flags are not explicitly populated.
- Standardizing access patterns mitigates unexpected key errors while preserving decoupled container-level configuration injection.

## Consequences

Positive:

- Zero external dependency footprint for build-time hooks and documentation generation modules.
- Direct configuration injection across local workstations, containerized environments, and automated pipelines.
- Predictable fallback behavior for non-critical operational settings such as verbosity levels.

Negative:

- Absence of centralized schema validation increases the likelihood of typos and discrepancies in configuration variable naming.
- Inconsistent retrieval semantics risk abrupt execution termination if mandatory variables are omitted without explicit error handling.

## Alternatives

- Adopt a centralized schema-driven configuration management library for all auxiliary scripts (rejected)
  Rejected because: Introduces excessive dependency overhead to isolated build and documentation scripts that only require basic environment flag resolution.
  When valid: When configuration structures become hierarchical, multi-tiered, or shared directly with the core application runtime.
- Hardcode configuration parameters within build and documentation scripts (rejected)
  Rejected because: Prevents dynamic parameterization across distinct developer workstations, container environments, and continuous integration runners.
  When valid: When values are strictly invariant and never differ across execution targets.

## Risks

- Unchecked environment variable access causes abrupt script failure when optional flags are missing.
  Mitigation: Enforce fallback default values or explicit error messaging on missing required keys.
  Owner: engineering team
- Environment variable name drift between build automation scripts and host execution pipelines.
  Mitigation: Maintain a documented catalog of all supported environment configuration parameters.
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
- Inspect the repository build scripts to identify all existing environment variable lookups and standardize fallback handling across modules.
- Ensure all required environment variables are documented in the project setup prerequisites.

## Continuation Context

Verify commands:

- Discover and run the project repository build verification script to confirm environment variable resolution succeeds under default conditions.
- Discover and execute the repository documentation build workflow with and without optional environment variables set.

Accept when:

- Build and documentation routines execute successfully with default fallback values when non-critical environment variables are unset.
- Missing mandatory environment variables produce clear, actionable error diagnostics rather than unhandled key lookup errors.

## Enforcement

- Verified by: Automated continuous integration pipeline execution across matrixed environment variable configurations.
- Verified by: Peer code review for pull requests modifying build hooks or documentation configuration routines.
- Violation handling: Build failures in automated validation pipelines when required environment variables fail to resolve.
- Violation handling: Rejection of pull requests introducing unchecked direct environment variable access without fallback defaults or error handling.
- Exception process: Submit an architecture review request justifying the requirement for unvalidated or ad-hoc environment access during emergency patches or legacy maintenance.
