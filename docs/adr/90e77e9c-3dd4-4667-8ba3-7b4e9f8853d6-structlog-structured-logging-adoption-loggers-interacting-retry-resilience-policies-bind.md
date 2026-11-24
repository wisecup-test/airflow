---
version: 2
name: 90e77e9c-3dd4-4667-8ba3-7b4e9f8853d6-structlog-structured-logging-adoption-loggers-interacting-retry-resilience-policies-bind
description: "structlog Structured Logging Adoption: Loggers Interacting Retry Resilience Policies Bind"
metadata:
  actual:
    adr_id: 90e77e9c-3dd4-4667-8ba3-7b4e9f8853d6
    status: accepted
    candidate_group_id: d5d966c3-8e38-4435-a3dd-84786d044d45
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/**
---

# structlog Structured Logging Adoption: Loggers Interacting Retry Resilience Policies Bind

Date: 2025-05-16
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `docs/**`
- `src/airflowctl/**`

## Context

- Client communications and operational request lifecycles require structured, contextual telemetry for observability, retry tracking, and request correlation.
- The standard library logging infrastructure provides unstructured text formatting, complicating structured data extraction and contextual field propagation.
- Structured logging is required to capture contextual key-value pairs during API transactions, network resilience events, and HTTP response evaluations.

## Problem Statement

Unstructured textual logging across API client and operational boundaries impairs telemetry parsing, prevents automated correlation of retry attempts, and complicates contextual diagnostic analysis during remote service interactions.

## Decision

- **R-L-001** SHOULD: Loggers interacting with retry resilience policies SHOULD bind structured event handlers and integrate with standard logging bridge functions for retry notification.

## Policy Block

In scope:

- Implementation of API client services, HTTP operations, network request retry layers, and remote service telemetry.

Out of scope:

- Static documentation generation scripts and basic command-line argument parser definitions that rely on standard library logging.

## Rationale

- Adopting structlog provides structured key-value telemetry that retains contextual request parameters, correlation identifiers, and runtime state across asynchronous or retried network boundaries.
- Using structlog.get_logger(logger_name=__name__) maintains hierarchical logger naming consistent with the module path while enabling structured rendering pipelines.
- Structured logging isolates diagnostic telemetry from raw standard output streams, facilitating integration with automated log collectors and monitoring platforms.

## Consequences

Positive:

- Standardized emission of contextual key-value fields across all API operations and client requests.
- Seamless correlation of retry events, status codes, and transient failures during external service interactions.
- Clear separation between command-line display formatting and internal operational diagnostic telemetry.

Negative:

- Introduces an external runtime dependency for logging alongside the standard library logging facility.
- Coexistence of structured and standard logging requires engineers to respect architectural component boundaries when selecting logger implementations.

## Alternatives

- Standard library logging exclusively across all modules (rejected)
  Rejected because: Standard library logging lacks native key-value contextual binding, requiring custom JSON formatters and manual context propagation across retries.
  When valid: Valid in zero-dependency script environments or standalone documentation toolchains.
- Custom JSON wrapper around standard library logging (rejected)
  Rejected because: Custom logging wrappers create bespoke maintenance burdens and lack standardized ecosystem middleware integrations for request pipelines.
  When valid: Valid when third-party runtime dependencies cannot be introduced into production deployments.

## Risks

- Inconsistent logger acquisition across modules leading to mixed structured and unstructured logs in client telemetry.
  Mitigation: Enforce linting rules and code review policies requiring structlog.get_logger across all operational client modules.
  Owner: engineering team
- Performance overhead from contextual dictionary allocation during high-frequency API polling loops.
  Mitigation: Utilize lazy evaluation and conditional log levels or suppression extensions for high-frequency requests.
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
- Configure structured loggers to attach contextual attributes at the point of request instantiation and propagate them through retry hooks.
- Ensure standard library logging handlers bridge warning and error events emitted by resilience decorators into structured collectors.

## Continuation Context

Verify commands:

- Discover the project test runner and execute the test suite covering client telemetry and logging configurations.
- Discover the static analysis tool configured in the repository and verify logger import consistency across API client modules.

Accept when:

- The test suite completes with all structured logging assertions passing without errors.
- Static analysis validates that API client and operational modules instantiate loggers using the structured logging factory.

## Enforcement

- Verified by: Automated continuous integration checks executing the project test suite and static analysis rules.
- Verified by: Peer code review verifying logger factory imports and contextual event binding on new API endpoints.
- Violation handling: Pull requests introducing unstructured logging or direct standard library loggers within client operational boundaries will be blocked.
- Violation handling: Violations identified during code review must be refactored to use the structured logging factory before merging.
- Exception process: Exceptions for non-operational utility scripts or standalone tooling must be documented and approved by the engineering leads.
