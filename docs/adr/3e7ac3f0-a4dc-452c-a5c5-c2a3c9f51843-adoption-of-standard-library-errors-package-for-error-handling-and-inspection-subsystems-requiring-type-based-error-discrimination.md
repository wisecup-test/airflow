---
version: 2
name: 3e7ac3f0-a4dc-452c-a5c5-c2a3c9f51843-adoption-of-standard-library-errors-package-for-error-handling-and-inspection-subsystems-requiring-type-based-error-discrimination
description: "Adoption of Standard Library errors Package for Error Handling and Inspection: Subsystems Requiring Type Based Error Discrimination"
metadata:
  actual:
    adr_id: 3e7ac3f0-a4dc-452c-a5c5-c2a3c9f51843
    status: accepted
    candidate_group_id: 2574cfc5-b33c-4e63-909a-4027ffd4b84b
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - cmd/airflow-go-pack/**
      - example/bundle/concurrentxcom/**
      - internal/bundlefooter/**
      - pkg/bundles/shared/**
      - pkg/edgeapi/**
      - pkg/execution/**
      - pkg/logging/server/**
      - pkg/worker/**
      - sdk/**
---

# Adoption of Standard Library errors Package for Error Handling and Inspection: Subsystems Requiring Type Based Error Discrimination

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `bundle/bundlev1/**`
- `cmd/airflow-go-pack/**`
- `example/bundle/concurrentxcom/**`
- `internal/bundlefooter/**`
- `pkg/bundles/shared/**`
- `pkg/edgeapi/**`
- `pkg/execution/**`
- `pkg/logging/server/**`
- `pkg/worker/**`
- `sdk/**`

## Context

- The software development kit coordinates distributed task execution, worker heartbeats, daemon communication, and plugin discovery across multi-process boundaries.
- Operations across inter-process coordinator channels, HTTP APIs, and bundle discovery encounter failure modes that require distinct programmatic handling by callers.
- Adopting the standard library errors module establishes a uniform mechanism for constructing, wrapping, and introspecting errors without introducing external dependencies.

## Problem Statement

The software development kit requires a consistent, zero-dependency mechanism to create, wrap, and inspect error conditions across disparate subsystems including worker runtimes, coordinator communication channels, and plugin registries. Without a standardized error model, packages risk relying on brittle string matching, failing to propagate underlying cause chains, or introducing conflicting third-party error libraries across public API contracts.

## Decision

- **R-LM-001** MUST: Subsystems requiring type-based error discrimination MUST use errors.As rather than direct type assertions to inspect wrapped error hierarchies.

## Policy Block

In scope:

- All internal and public software development kit packages that instantiate, propagate, or inspect error conditions.
- Inter-process communication adapters, worker execution runtimes, and bundle discovery modules handling fallible operations.

Out of scope:

- External protocol message payloads exchanged over serialization boundaries where errors must be encoded as transport data structures rather than language error types.

## Rationale

- Using the standard library errors package provides native support for causal wrapping and unwrapping without introducing external module dependencies into the core SDK distribution.
- Standardizing on errors.As and errors.Is allows downstream callers to introspect specific error conditions across nested layers of coordinator and network abstraction.
- Evidence across execution, bundle, and worker modules demonstrates consistent reliance on standard error wrapping to preserve debugging context and drive operational retry or cancellation decisions.

## Consequences

Positive:

- Eliminates third-party dependency overhead for core error management across all software development kit modules.
- Maintains complete causal chains through wrapped errors, facilitating debugging and observability in distributed worker environments.
- Enables robust, type-safe error introspection across API boundaries using errors.As and errors.Is.

Negative:

- Requires continuous discipline across contributors to consistently wrap errors with formatting directives rather than discarding error context.
- Deeply nested wrapped error chains require deliberate inspection logic to avoid leaking internal package implementation details across public boundaries.

## Alternatives

- Adoption of external third-party error wrapping libraries (rejected)
  Rejected because: Introduces external dependency overhead into the public software development kit contract when standard library capabilities fully meet error wrapping and inspection requirements.
  When valid: Valid when targeting legacy language runtime environments that lack native causal wrapping or structured inspection capabilities.
- Ad-hoc string formatting for error propagation (rejected)
  Rejected because: Destroys underlying error types, preventing programmatic recovery via errors.As or errors.Is and forcing downstream callers to rely on brittle string matching.
  When valid: Valid only in standalone command-line entry points where error output is solely intended for human-readable terminal display.

## Risks

- Developers might inadvertently format error messages without wrapping directives, dropping root cause context.
  Mitigation: Enforce static analysis linting checks during repository continuous integration to detect unwrapped error propagation.
  Owner: engineering team
- Internal error structures could be exposed directly to public API consumers through errors.As traversal.
  Mitigation: Design deliberate public sentinel errors and sanitize internal transport error types before surfacing across boundary interfaces.
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
- Define package-level sentinel errors using errors.New for expected domain failure conditions, ensuring exported names follow standard SDK naming conventions.
- When handling network or coordinator protocol responses, extract concrete error payload types using errors.As before falling back to generic communication error handling.

## Continuation Context

Verify commands:

- Discover the repository static analysis configuration and run linters to detect improper error formatting and ignored error returns.
- Discover the project test suite runner from the build configuration and execute unit and integration tests verifying error wrapping and unwrapping behavior.

Accept when:

- All unit and integration test suites pass with assertions verifying error identities and causal chains.
- Static analysis linters pass without flagging any uninspected errors or improper error-wrapping directives.

## Enforcement

- Verified by: Automated continuous integration pipeline running test suites and static analysis linters on all pull requests.
- Verified by: Peer code review verifying compliance with error creation, wrapping, and inspection rules.
- Violation handling: Pull requests containing unwrapped error chains, direct type assertions on errors, or external error library dependencies will be blocked until remediated.
- Exception process: Exceptions for specialized transport serialization layers must be reviewed and approved by the core architecture review team and documented in the module design notes.
