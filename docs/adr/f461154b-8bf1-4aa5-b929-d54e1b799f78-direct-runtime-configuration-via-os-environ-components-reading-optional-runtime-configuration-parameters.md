---
version: 2
name: f461154b-8bf1-4aa5-b929-d54e1b799f78-direct-runtime-configuration-via-os-environ-components-reading-optional-runtime-configuration-parameters
description: "Direct Runtime Configuration via os.environ: Components Reading Optional Runtime Configuration Parameters"
metadata:
  actual:
    adr_id: f461154b-8bf1-4aa5-b929-d54e1b799f78
    status: accepted
    candidate_group_id: 1cff565b-8a7d-4b76-96f4-c5a74327b13f
    evidence:
      source: deep
  scope:
    root: performance
    paths:
      - src/performance_dags/performance_dag/**
---

# Direct Runtime Configuration via os.environ: Components Reading Optional Runtime Configuration Parameters

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `performance`

Applies to:
- `src/performance_dags/performance_dag/**`

## Context

- Execution workflows require runtime parameters such as task counts, execution shapes, and schedule intervals to be configurable externally without modifying source code.
- The codebase accesses environment variables directly through the standard os.environ mapping rather than introducing external configuration management libraries.
- The observed pattern is restricted to an isolated workload generation module with single-file scope, utilizing direct dictionary access for required variables and fallback getters for optional flags.

## Problem Statement

Workload execution components require dynamic runtime configuration for scale, timing, and execution topology, but introducing dedicated external configuration parsing frameworks adds unnecessary packaging overhead to lightweight execution scripts.

## Decision

- **R-CEM-001** MUST: Components reading optional runtime configuration parameters MUST use os.environ.get with explicit fallback default values.

## Policy Block

In scope:

- Execution scripts and runtime modules that configure workload parameters through environment variables.

Out of scope:

- Application layers requiring structured hierarchical configuration files, schemas, or remote secret management.

## Rationale

- Direct use of os.environ avoids third-party dependencies for runtime configuration in standalone execution scripts.
- Distinguishing between mandatory dictionary access and optional getter access with defaults establishes clear operational contracts for execution environments.

## Consequences

Positive:

- Zero external dependencies required for reading runtime operational configurations.
- Immediate failure on missing mandatory parameters via standard dictionary key error behavior.

Negative:

- Lack of built-in type coercion requires manual conversion for numeric or boolean environment variables.
- Absence of centralized validation schema increases risk of runtime type errors downstream.

## Alternatives

- External schema-based configuration libraries (rejected)
  Rejected because: Introduces external dependencies and build overhead disproportionate to the requirements of lightweight execution scripts.
  When valid: When complex nested structures, multi-source layered configs, or automated schema validations are required across multiple microservices.
- Static file-based configuration loading (rejected)
  Rejected because: Reduces flexibility in dynamic containerized execution environments where variables are injected per run.
  When valid: When configurations are strictly static and tracked directly under version control.

## Risks

- Unvalidated string values from environment variables may lead to type conversion errors at runtime.
  Mitigation: Explicitly parse and validate data types immediately upon extraction from os.environ.
  Owner: engineering team
- Missing mandatory environment variables halt execution unexpectedly if not documented in runtime contracts.
  Mitigation: Document all mandatory environment variable keys and fail fast at module initialization.
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
- Parse and validate primitive types immediately after extraction from os.environ before passing values to downstream execution functions.
- Maintain consistent prefix naming for all environment variables within the same execution domain to prevent environment collision.

## Continuation Context

Verify commands:

- Discover and run the project static analysis and linting suite to verify compliant usage of os.environ and os.environ.get.
- Discover and run the automated test suite with missing and populated environment variables to verify fail-fast and fallback behaviors.

Accept when:

- Static analysis validates that all optional environment variable reads specify a fallback default.
- Test suites pass with expected errors raised when mandatory environment variables are omitted.

## Enforcement

- Verified by: Automated linting and static analysis checks during integration pipelines.
- Verified by: Peer code review for execution scripts accessing runtime configuration.
- Violation handling: Static analysis failures block merging of code accessing environment variables without required fallbacks or proper indexing.
- Exception process: Exceptions must be documented with an architectural justification and approved by the engineering leads.
