---
version: 2
name: b464fb4d-00f2-478e-a2c9-86364871f830-adoption-of-log-slog-as-core-structured-logging-module-log-messages-emitted-structured-attributes-typed
description: "Adoption of log/slog as Core Structured Logging Module: Log Messages Emitted Structured Attributes Typed"
metadata:
  actual:
    adr_id: b464fb4d-00f2-478e-a2c9-86364871f830
    status: accepted
    candidate_group_id: e42e41fd-ce27-49fc-9568-0f2b23e0bc47
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - edge/**
      - example/bundle/**
      - pkg/bundles/shared/**
      - pkg/config/**
      - pkg/execution/**
      - pkg/logging/**
      - pkg/worker/**
---

# Adoption of log/slog as Core Structured Logging Module: Log Messages Emitted Structured Attributes Typed

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `bundle/bundlev1/**`
- `edge/**`
- `example/bundle/**`
- `pkg/bundles/shared/**`
- `pkg/config/**`
- `pkg/execution/**`
- `pkg/logging/**`
- `pkg/worker/**`

## Context

- Multiple subsystems within the SDK require consistent diagnostic, error, and lifecycle event logging across execution engines, worker pools, and communication dispatchers.
- The codebase previously mixed unstructured format logging with external logging interfaces, creating fragmented log sinks and inconsistent attribute schemas.
- Static analysis identifies eighteen files across worker, execution, configuration, and plugin boundaries standardizing on log/slog for structured log emission.
- The architecture requires composable log dispatching capable of multiplexing outputs between system supervisors and workload-specific execution logs.

## Problem Statement

Inconsistent logging abstractions and unstructured string formatting across distributed worker nodes, execution dispatchers, and plugin boundaries obscure operational observability and impede structured metric extraction. Without a standardized structured logging module and handler abstraction, diagnostic records cannot be safely correlated across asynchronous goroutines or multiplexed between supervisor processes and individual task artifact logs.

## Decision

- **R-LM-001** MUST: Log messages MUST be emitted with structured attributes using typed key-value pairs or slog.Attr declarations rather than formatted string interpolation.

## Policy Block

In scope:

- All internal and public packages comprising the worker runtime, execution supervisor, communication dispatchers, and plugin servers.
- Diagnostic, operational, and task workload event generation across all execution components.

Out of scope:

- End-user user interface rendering outside the backend execution environment.
- Raw binary communication stream framing where structured logging is not applicable.

Exceptions:

- **EXC-20-001** Integration with external sub-process runners or third-party plugins requires native unstructured stream pipes before translation into structured logging.
  Approval: Principal Architect or SDK Maintainer
  Documentation: Document adapter boundary behavior and translation mapping in component architecture notes.

## Rationale

- Adopting log/slog standardizes structured key-value telemetry across eighteen files without introducing heavyweight external logging dependencies into the core SDK.
- The slog.Handler interface enables straightforward composition of multiplexed loggers, such as teeing supervisor telemetry and isolated task execution logs.
- Standardizing on context-aware log/slog calls ensures contextual propagation across concurrent task runners and asynchronous message dispatchers.

## Consequences

Positive:

- Provides unified, schema-aligned structured log records across worker processes, communication handlers, and plugin servers.
- Enforces composable handler pipelines, including simultaneous emission to console streams and structured file sinks.
- Minimizes external dependency footprint by relying on standard library structured logging capabilities.

Negative:

- Requires wrapping third-party and legacy logging interfaces with dedicated translation adapters.
- Enforces stricter parameter conventions that prevent rapid unstructured string concatenation.
- Introduces minor allocation overhead when composing complex structured attributes if not properly typed.

## Alternatives

- Third-party structured logging framework (rejected)
  Rejected because: Imposes heavy transitive dependencies on all downstream SDK consumers and creates dependency version conflicts across client applications.
  When valid: Valid only in standalone services where transitive dependency isolation is not a constraint.
- Standard unstructured formatting and print packages (rejected)
  Rejected because: Lacks structured key-value fields, context propagation, and composable handler pipelines necessary for multi-tenant task instance isolation.
  When valid: Valid only in minimal scratch utilities or trivial throwaway scripts.

## Risks

- Performance degradation from excessive attribute allocation in high-throughput message loops.
  Mitigation: Use typed slog.Attr constructors and guard high-frequency debug logging with level enablement checks.
  Owner: Core SDK Engineering Team
- Third-party plugins failing to integrate with slog.Handler abstractions.
  Mitigation: Provide and maintain dedicated adapter bridges for external logger interfaces.
  Owner: Plugin Architecture Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Configure root logger handlers during application bootstrap and inject slog.Logger instances down to subsystem controllers via constructors.
- When streaming task execution logs separately from daemon operations, compose a multiplexed slog.Handler that branches records to both the supervisor sink and the task-specific writer.

## Continuation Context

Verify commands:

- Discover and execute the repository unit test suite across all packages to verify logging handlers and bridge behaviors.
- Discover and execute the repository static analysis and linting tools to check for banned logging imports and verify structured attribute compliance.

Accept when:

- All packages compile and pass repository test suites without importing unauthorized logging packages.
- All log emission call sites supply structured key-value attributes and pass static analysis checks.
- Custom handler implementations multiplex and route log records to expected output destinations during component verification.

## Enforcement

- Verified by: Automated continuous integration lint checks that flag imports of disallowed logging packages.
- Verified by: Code review inspection ensuring structured attribute usage and context propagation.
- Violation handling: Pull requests containing disallowed logging imports or unstructured logging calls will be blocked by automated continuous integration gates.
- Violation handling: Violations discovered in main branches must be remediated immediately via prioritized refactoring tasks.
- Exception process: Submit an architectural exception request detailing technical constraints requiring alternative logging behavior.
- Exception process: Obtain formal sign-off from the technical lead and record exception justification in component documentation.
