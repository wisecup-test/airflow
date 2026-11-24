---
version: 2
name: 17149e46-997c-4f21-bde3-d2cb91e6fb12-structured-logging-adoption-via-structlog-core-modules-service-boundaries-background-workers
description: "Structured Logging Adoption via structlog: Core Modules Service Boundaries Background Workers"
metadata:
  actual:
    adr_id: 17149e46-997c-4f21-bde3-d2cb91e6fb12
    status: accepted
    candidate_group_id: d1769a16-5948-4d7e-a203-b592df67cd96
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Structured Logging Adoption via structlog: Core Modules Service Boundaries Background Workers

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Backend services, API routes, execution engines, and asynchronous triggers require uniform diagnostic telemetry and structured event logging.
- String-interpolated logging formats hinder automated log aggregation, metric extraction, and machine filtering in distributed orchestration environments.
- Static analysis across 35 files demonstrates widespread adoption of structlog as the primary logging module across core runtime and API domains.

## Problem Statement

Unstructured and disparate logging practices across API endpoints, orchestrator models, and background workers result in inconsistent log formats, complicating log analysis, metric derivation, and root-cause investigation in distributed environments.

## Decision

- **R-LM-001** MUST: All core modules, service boundaries, background workers, and API route handlers MUST use structlog as the standard structured logging library for emitting diagnostics, state changes, and runtime events.

## Policy Block

In scope:

- All backend application services, HTTP API route controllers, execution workers, schedulers, and asynchronous triggers.
- All new modules introducing diagnostic telemetry or event logging.

Out of scope:

- Isolated client-side scripts running outside the core application runtime environment.
- Vendor-supplied third-party packages with immutable logging interfaces.

Exceptions:

- **EXC-20-001** Legacy third-party integration bridges require standard logging handler adapters.
  Approval: Platform architecture team approval
  Documentation: Architecture exception note in the affected module header

## Rationale

- Structured logging via structlog provides seamless key-value context binding essential for tracing asynchronous operations and distributed scheduler runs.
- Standardizing on structlog.get_logger eliminates conflicting logging configurations and unifies log formatting across monolithic and service components.
- Empirical evidence from 35 files demonstrates established repository-wide adoption across routes, models, services, and triggers.

## Consequences

Positive:

- Uniform machine-readable log output across API servers, orchestrator schedulers, and asynchronous workers.
- Contextual key-value attributes enable fast indexing, query filtering, and aggregation in log observability infrastructure.
- Consistent logger instantiation pattern prevents disparate logging configurations across subsystem boundaries.

Negative:

- Introduces an external runtime dependency that must be maintained and tracked across repository environments.
- Developers accustomed to printf-style or format-string logging must adopt structured key-value conventions.
- Improper passing of non-serializable objects as structured context can trigger logging serialization exceptions if not guarded.

## Alternatives

- Standard library logging module with format strings (rejected)
  Rejected because: Lacks native structured key-value semantics, requiring custom JSON formatters and error-prone string interpolation that degrades downstream log searchability.
  When valid: Acceptable in standalone minimal scripts with no distributed tracing or observability platform dependencies.
- Ad-hoc JSON serialization wrappers around standard logging calls (rejected)
  Rejected because: Introduces redundant boilerplate serialization, inconsistent dictionary keys, and performance overhead across asynchronous event loops.
  When valid: Useful when operating in locked runtime environments where third-party libraries cannot be installed.

## Risks

- Developers inadvertently reverting to string interpolation, discarding structured key-value benefits.
  Mitigation: Establish linting rules preventing string interpolation in log statements and mandate keyword arguments.
  Owner: Engineering Team
- Logging sensitive payload data through unrestricted keyword context dictionaries.
  Mitigation: Ensure structured logging processors sanitize or omit sensitive contextual keys before emission.
  Owner: Security Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Initialize loggers at the top of each module using structlog.get_logger passing the current module name.
- Always supply dynamic event context as discrete keyword parameters to log methods rather than constructing formatted strings.

## Continuation Context

Verify commands:

- Discover the repository static analysis and linting execution script from the workspace build configuration and run it to verify logger import conformity.
- Discover the repository test runner script and execute the test suite across affected modules to validate structured telemetry behavior.

Accept when:

- All module-level logging instances in backend, runtime, and API components initialize exclusively via structlog.get_logger without standard library logger bypasses.
- Automated repository test and static inspection checks pass with zero unstructured string formatting violations in log emission calls.

## Enforcement

- Verified by: Automated static analysis checks enforcing logger import and instantiation rules in continuous integration.
- Verified by: Peer code reviews verifying structured key-value keyword usage instead of formatted string templates.
- Violation handling: Pull requests with unauthorized logger imports or unformatted logging invocations fail continuous integration gates.
- Violation handling: Violating code must be refactored to use structlog logger instances with structured attributes before approval.
- Exception process: Submit an architecture review request detailing why structured logging cannot meet component performance or dependency constraints.
- Exception process: Obtain formal approval from the platform engineering maintainers before merging non-conforming telemetry implementations.
- Exception process: Document any approved exception with an architectural justification in the repository architecture register.
