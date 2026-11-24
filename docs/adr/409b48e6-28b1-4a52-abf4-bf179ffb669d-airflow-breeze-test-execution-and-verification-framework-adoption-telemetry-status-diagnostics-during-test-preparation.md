---
version: 2
name: 409b48e6-28b1-4a52-abf4-bf179ffb669d-airflow-breeze-test-execution-and-verification-framework-adoption-telemetry-status-diagnostics-during-test-preparation
description: "airflow_breeze Test Execution and Verification Framework Adoption: Telemetry Status Diagnostics During Test Preparation"
metadata:
  actual:
    adr_id: 409b48e6-28b1-4a52-abf4-bf179ffb669d
    status: accepted
    candidate_group_id: 8f3790de-31a9-430f-af29-3ed5449978f8
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# airflow_breeze Test Execution and Verification Framework Adoption: Telemetry Status Diagnostics During Test Preparation

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`

## Context

- Dispersed integration and release qualification test suites require uniform path resolution, environment verification, and argument mapping across multiple test suites.
- Direct invocation of disparate underlying test runners leads to divergent execution flags, uninspected container images, and fragile pre-release validation.
- The internal airflow_breeze module provides centralized test routing, pre-flight environment checks, and release candidate verification contracts.

## Problem Statement

Managing complex integration test suites, container image inspections, and release candidate qualifications across diverse developer workstations and continuous integration environments causes configuration drift and unreliable test runs when executed through uncoordinated ad-hoc scripts.

## Decision

- **R-TS-001** MUST: Telemetry and status diagnostics during test preparation and execution MUST be routed through structured console printing protocols.

## Policy Block

In scope:

- All test execution orchestration, test path resolution, containerized integration test dispatch, and release qualification workflows within developer tooling.

Out of scope:

- Isolated unit tests executing strictly within isolated third-party client libraries without orchestration requirements.

## Rationale

- Centralizing test dispatch into internal airflow_breeze orchestration utilities ensures that argument mappings, path discovery, and pre-execution image verification remain strictly identical across all environments.
- Standardized validation protocols prevent incomplete or uninspected artifacts from entering integration test runs and release qualification sequences.
- Shared console telemetry routines maintain consistent error reporting and exit handling across execution phases.

## Consequences

Positive:

- Centralizes test path resolution and test type argument mapping across integration and release validation boundaries.
- Enforces automated pre-test image verification and release qualification checks before test dispatch occurs.
- Standardizes structured console reporting and diagnostic output across developer test executions.

Negative:

- Changes to test dispatch logic require updates within the internal orchestration framework rather than local ad-hoc script modifications.
- Adds an internal abstraction layer over low-level test runner arguments and environment checks that engineers must understand.

## Alternatives

- Ad-hoc decentralized test runner scripts invoked directly by developer environments (rejected)
  Rejected because: Produces divergent test flags, untracked environmental dependencies, and inconsistent test type argument mappings across development and continuous integration environments.
  When valid: Acceptable for lightweight single-module repositories without containerized integration requirements.
- Pure container image entrypoint scripts without a structured orchestration library (rejected)
  Rejected because: Lacks reusable path resolution utilities, programmatic verification contracts, and structured console telemetry.
  When valid: Applicable when test execution requires zero host-side argument transformation or pre-test image inspection.

## Risks

- Centralized argument mapping dictionaries becoming out of sync with underlying test runner capabilities.
  Mitigation: Ensure test argument mappings and verification protocols are comprehensively covered by automated unit tests within the developer tooling suite.
  Owner: Engineering Team
- Pre-execution image inspection checks failing due to environment-specific container daemon configurations.
  Mitigation: Isolate environment and container inspection contracts into modular functions with clear error reporting and graceful failure modes.
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Locate the internal testing orchestration utilities within the repository to reference existing test type mappings and verification contracts before registering new test suites.
- Utilize established console reporting interfaces for emitting diagnostic and error states during test preparation and execution phases.

## Continuation Context

Verify commands:

- Discover and run the project test execution verification suite using the repository-defined test runner command.
- Execute repository static analysis and linting checks against developer tooling test utilities to confirm contract compliance.

Accept when:

- The project test execution and verification suite passes across all configured test types and validation routines.
- Test path resolution and argument mapping interfaces execute without unhandled configuration exceptions.
- Pre-execution environment and image verification contracts successfully validate deployment readiness before test dispatch.

## Enforcement

- Verified by: Automated continuous integration pipeline execution verifying all test suites pass through standardized orchestration wrappers.
- Verified by: Static analysis and peer review ensuring no direct, unmediated test runner execution scripts bypass the centralized testing utilities.
- Violation handling: Build failures triggered on pull requests that bypass centralized test path resolution or pre-execution validation routines.
- Violation handling: Rejection of release qualification workflows that omit standard release candidate verification protocols.
- Exception process: Submit an architectural exception request detailing why a specific test suite or validation routine cannot utilize the centralized orchestration contracts.
- Exception process: Obtain approval from the testing and release management engineering maintainers before implementing custom invocation wrappers.
