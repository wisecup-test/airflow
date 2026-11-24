---
version: 2
name: 3c3d28ed-d7a0-448a-8838-27fb52984ee7-traceback-module-adoption-for-stack-trace-formatting-and-diagnostics-serialization-before-implementing-modifying-exception-handling-components
description: "traceback Module Adoption for Stack Trace Formatting and Diagnostics Serialization: Before Implementing Modifying Exception Handling Components"
metadata:
  actual:
    adr_id: 3c3d28ed-d7a0-448a-8838-27fb52984ee7
    status: accepted
    candidate_group_id: b0a4104a-d002-456c-a245-2aedccc1b311
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# traceback Module Adoption for Stack Trace Formatting and Diagnostics Serialization: Before Implementing Modifying Exception Handling Components

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Subsystem boundaries, including HTTP endpoints, asynchronous event triggers, and background processing workers, encounter uncaught exceptions and database errors that must be captured and transferred across execution contexts.
- Raw exception objects cannot be directly serialized across transport boundaries or persisted safely to relational datastores without prior string or structural conversion.
- Core runtime components require structured diagnostic information tied to correlated lookup identifiers while preventing sensitive stack trace disclosures to untrusted API consumers.

## Problem Statement

Propagating raw exception instances across process boundaries, asynchronous queues, and HTTP interfaces causes serialization failures and leaks internal implementation details to external consumers. The system requires a consistent, standard mechanism to extract, format, and redact stack traces into portable string representations at boundary layers without relying on external diagnostic dependencies.

## Decision

- **R-LM-001** MUST: Before implementing or modifying exception handling components that depend on external or runtime libraries, engineers MUST inspect the project lock and resolution artifacts to confirm the exact resolved dependency versions and verify API compatibility.

## Policy Block

In scope:

- Exception handling routines operating at API server boundaries.
- Asynchronous trigger workers and event generators yielding failure payloads.
- Background file processing and parsing modules recording diagnostic errors for persistence.

Out of scope:

- Internal control flow where exceptions are caught and resolved immediately within the same function scope.
- Performance-critical inner loops where stack trace generation introduces measurable latency.

Exceptions:

- **EXC-20-001** A third-party monitoring integration captures and serializes raw exception frames directly via its own native instrumentation.
  Approval: Architecture Review Board
  Documentation: Architecture decision exception record documenting third-party diagnostic integration.

## Rationale

- The codebase exhibits repeated, consistent adoption of traceback.format_tb and traceback.format_exception across five distinct files spanning API exception handling, triggers, and collection workers.
- Using the standard traceback module avoids heavy external dependencies for error stringification while providing complete fidelity for debugging and correlation across asynchronous workers.
- Standardized stack trace string extraction enables centralized logging with correlated unique identifiers, ensuring secure error reporting to external clients while preserving internal diagnostic depth.

## Consequences

Positive:

- Provides consistent and portable diagnostic representations across asynchronous triggers, HTTP endpoints, and database error stores.
- Maintains a zero external dependency footprint for stack trace extraction and error string conversion.
- Improves security posture through separation of internal correlated stack traces from sanitized external error responses.

Negative:

- Traceback string generation incurs string allocation and formatting overhead on hot error execution paths.
- Serialized stack trace strings increase payload footprint when transmitted across message channels or stored in persistence stores.

## Alternatives

- Direct serialization of raw exception instances across transport boundaries (rejected)
  Rejected because: Exception instances cannot be reliably serialized across process or network boundaries and risk deserialization vulnerabilities.
  When valid: In-process single-threaded synchronous error handling where no transport boundary exists.
- Third-party stack trace enhancement libraries (rejected)
  Rejected because: Introduces external runtime dependencies into core processing and utility components that require minimal footprint.
  When valid: Standalone local development debugging utilities and interactive environments.

## Risks

- Unsanitized stack traces leaking proprietary internal details or database query structures to external clients.
  Mitigation: Enforce error masking in HTTP exception handlers by emitting generic client messages with correlation identifiers, revealing details only under explicit configuration.
  Owner: engineering team
- Memory overhead and unbounded string sizes from deeply recursive call stack traces.
  Mitigation: Apply size limits or line truncations on serialized traceback strings before persistence or event emission.
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
- Extract traceback strings at the immediate boundary where an exception is caught, binding the resulting string to the outgoing event or response dictionary.
- Combine formatted traceback output with unique correlation identifiers in server-side logs to facilitate tracing without exposing stack internals over public endpoints.

## Continuation Context

Verify commands:

- Discover the project static analysis and linting configuration from repository manifests and run the suite to verify compliance with core library import standards.
- Discover the automated test runner configured in the repository and execute test suites covering boundary exception handlers to verify traceback serialization behavior.

Accept when:

- Static analysis passes with no unauthorized error formatting routines or direct raw exception transports across boundary modules.
- Automated tests confirm that exception handlers produce correlated log entries and sanitized responses across boundary interfaces.

## Enforcement

- Verified by: Continuous integration static analysis checks verifying standardized error handling and core library imports.
- Verified by: Peer code reviews covering boundary interfaces, HTTP handlers, and asynchronous event triggers.
- Violation handling: Pull requests containing direct raw exception transmission or custom stack trace string parsing will be blocked in review.
- Violation handling: Automated linting failures will reject builds that bypass standardized boundary error handlers.
- Exception process: Submit an architectural review request outlining performance constraints or specialized third-party diagnostic integration requirements for formal review.
