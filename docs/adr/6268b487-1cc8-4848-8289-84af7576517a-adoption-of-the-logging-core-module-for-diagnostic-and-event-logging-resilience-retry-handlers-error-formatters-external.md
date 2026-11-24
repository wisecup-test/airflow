---
version: 2
name: 6268b487-1cc8-4848-8289-84af7576517a-adoption-of-the-logging-core-module-for-diagnostic-and-event-logging-resilience-retry-handlers-error-formatters-external
description: "Adoption of the logging Core Module for Diagnostic and Event Logging: Resilience Retry Handlers Error Formatters External"
metadata:
  actual:
    adr_id: 6268b487-1cc8-4848-8289-84af7576517a
    status: accepted
    candidate_group_id: 15b1d632-8fff-47d3-86dc-22c1a699cf3b
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/**
---

# Adoption of the logging Core Module for Diagnostic and Event Logging: Resilience Retry Handlers Error Formatters External

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `docs/**`
- `src/airflowctl/**`

## Context

- Application components spanning documentation build scripts, command-line parsing interfaces, and API client subsystems require consistent diagnostic event emission and error tracking.
- Decoupled modules need a standardized interface for logger acquisition without imposing rigid proprietary transport bindings on individual components.
- The codebase exhibits widespread reliance on the core logging module and its getLogger interface to establish namespace-hierarchical event logging and standard log-level constants across distinct functional layers.

## Problem Statement

Without a standardized logging module contract, heterogeneous subsystems across CLI parsing, API client networking, and documentation generation risk fragmenting diagnostic outputs across inconsistent logging mechanisms, making centralized error tracking and level-based filtering unmanageable.

## Decision

- **R-LM-001** SHOULD: Resilience retry handlers, error formatters, and external hooks consuming severity levels SHOULD reference standard logging module level constants.

## Policy Block

In scope:

- Internal application modules requiring diagnostic event capture, operational logging, or warning emission.
- Subsystems interfacing with retry handlers, error handlers, or documentation processors that consume log levels.

Out of scope:

- Direct user-facing console output streams intended strictly for command display rather than diagnostic tracking.

## Rationale

- Standardizing on the logging module ensures a uniform diagnostic API across disparate modules while allowing root loggers and handlers to be configured centrally.
- Using the getLogger factory provides hierarchical namespace inheritance, enabling selective log filtering and suppression without altering individual component logic.
- Adopting core logging level constants allows cross-cutting resilience mechanisms, such as retry callbacks and error formatters, to interact seamlessly with diagnostic infrastructure.

## Consequences

Positive:

- Provides unified diagnostic visibility across command parsing, client interactions, and build configurations.
- Enables hierarchical log filtering and level-based suppression through standardized module namespaces.
- Facilitates clean integration with third-party extensions and structured logging adapters.

Negative:

- Requires disciplined logger scoping across all modules to prevent namespace collisions or orphan loggers.
- May necessitate bridging configurations when combined with structured logging libraries.

## Alternatives

- Direct unformatted output to standard streams (rejected)
  Rejected because: Lacks hierarchical log-level filtering, structured suppression capabilities, and diagnostic severity classification.
  When valid: Minimal single-file utility scripts with no external consumers or operational monitoring requirements.
- Exclusive use of third-party structured logging frameworks across all subsystems (rejected)
  Rejected because: Introduces external dependency overhead to build tools and simple parser utilities that only require standard diagnostic hooks.
  When valid: High-throughput microservices where strictly structured JSON log aggregation is an absolute operational requirement.

## Risks

- Inconsistent logger naming across modules leading to fragmented hierarchy and lost log events.
  Mitigation: Enforce module-scoped naming conventions and verify logger acquisition through automated static analysis.
  Owner: Engineering Team
- Logger configuration collision between core logging handlers and structured logging wrappers.
  Mitigation: Define clear boundary adapters between core logging handlers and structured logging interceptors.
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
- Acquire module-level logger instances at module initialization time and pass standard logging severity constants to resilience decorators and retry hooks.
- Ensure structured logging wrappers attach correlation identifiers while preserving standard logging hierarchy behavior.

## Continuation Context

Verify commands:

- Discover and run the project static analysis and linting checks to verify module logger acquisition compliance.
- Discover and execute the repository test suite to ensure diagnostic logging and log-level assertions pass.

Accept when:

- All modules acquire loggers via the approved factory interface without direct instantiation.
- Automated test suites and static analysis checks pass with zero logging-related errors or warnings.

## Enforcement

- Verified by: Automated static analysis and linting rules executed in continuous integration pipelines.
- Verified by: Peer code review verification of new modules and logging integration points.
- Violation handling: Automated continuous integration build failure on non-compliant logger acquisitions.
- Violation handling: Code review rejection of unapproved logging patterns or direct stream writing.
- Exception process: Formal architectural review request documenting technical necessity and alternative mitigation strategies.
