---
version: 2
name: b7af8467-84c4-46e6-a7d1-6538c360bd66-subsystem-log-filtering-via-logging-getlogger-and-addfilter-filter-implementations-registered-addfilter-inspect-record
description: "Subsystem Log Filtering via logging.getLogger and addFilter: Filter Implementations Registered Addfilter Inspect Record"
metadata:
  actual:
    adr_id: b7af8467-84c4-46e6-a7d1-6538c360bd66
    status: accepted
    candidate_group_id: 9b7a903b-d767-4ba2-9643-99dafc4b5473
    evidence:
      source: deep
  scope:
    root: docker-stack-docs
---

# Subsystem Log Filtering via logging.getLogger and addFilter: Filter Implementations Registered Addfilter Inspect Record

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `docker-stack-docs`

Applies to:
- `**` (whole root)

## Context

- Build and execution environments generate high volumes of repetitive diagnostic warnings from third-party extension packages during documentation compilation.
- Broad root-level log suppression obscures genuine configuration errors and syntax warnings across the execution lifecycle.
- Targeted logger acquisition enables selective suppression of known non-critical entries while preserving standard observability across the remaining system components.

## Problem Statement

External subsystems and documentation parsing extensions emit non-actionable diagnostic messages and known benign warnings that clutter automated build logs. Suppressing these messages by raising the global log level risks hiding critical runtime errors from other subsystems, necessitating a mechanism to selectively intercept and filter log records from specific logger namespaces without affecting overall log verbosity.

## Decision

- **R-L-001** SHOULD: Filter implementations registered with addFilter SHOULD inspect record attributes deterministically and return boolean flags without mutating log record payload state.

## Policy Block

In scope:

- Subsystem log handling in automated documentation build scripts
- Integration points where external extension namespaces emit non-critical diagnostic noise

Out of scope:

- Production application request logging and audit event pipelines
- Global root logger initialization and output formatting handlers

## Rationale

- Scoped interception using logging.getLogger prevents diagnostic message pollution without modifying log levels for the remainder of the execution environment.
- Registering filter callbacks via addFilter provides deterministic record-level evaluation based on message content, source path, or event attributes.
- Confining log manipulation to the specific third-party namespace preserves existing diagnostic visibility for primary application modules.

## Consequences

Positive:

- Eliminates non-actionable log noise from specific external extensions during documentation generation.
- Preserves full diagnostic visibility and default log levels across all unrelated modules and packages.
- Enables fine-grained inspection and suppression of individual log records without altering global logger handlers.

Negative:

- Creates direct coupling between documentation configuration and internal logger naming conventions of external libraries.
- Upstream changes to third-party logger namespaces or log message structures can silently invalidate filter rules.
- Introduces execution overhead for evaluating filter predicates on each dispatched log record in the targeted hierarchy.

## Alternatives

- Elevating the global log level threshold to suppress all warning and info records (rejected)
  Rejected because: Suppresses critical error and warning visibility across all unrelated application modules, preventing detection of valid build anomalies
  When valid: Only when entire execution runs require total silence and errors are captured exclusively via return exit codes
- Shell-level post-processing or pipeline output stream filtering (rejected)
  Rejected because: Couples build reliability to shell pipelines, masks process exit statuses, and bypasses language-level structured logging features
  When valid: When third-party libraries write directly to raw output descriptors without using the standard logging infrastructure

## Risks

- External dependency refactors internal logger namespace, causing filter attachment to target an inactive logger
  Mitigation: Verify active logger identifiers during dependency lock version updates and inspect documentation build logs for resurfaced warnings
  Owner: engineering team
- Overly broad filter matching criteria unintentionally discard legitimate syntax or configuration errors
  Mitigation: Enforce strict predicate checks on message text and record attributes rather than blanket message rejection
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
- Implement filter callbacks with minimal computational overhead as they execute synchronously within the logger dispatch pipeline.
- Ensure filter functions handle unexpected record attribute formats gracefully without raising unhandled exceptions.

## Continuation Context

Verify commands:

- Discover the project documentation build script from the repository configuration and execute it to verify that build logs run cleanly without targeted third-party warning cascades.
- Discover the test runner from the repository manifest and execute the test suite covering documentation utilities to validate filter predicate logic.

Accept when:

- The documentation build execution completes with zero spurious warnings from the targeted third-party namespace.
- Standard error output retains all legitimate diagnostic and failure warnings emitted by core modules.

## Enforcement

- Verified by: Continuous integration documentation build log inspection
- Verified by: Pull request peer review of logging configuration changes
- Violation handling: Build pipeline failure on unhandled diagnostic errors
- Violation handling: Rejection of pull requests introducing global log suppression in localized scripts
- Exception process: Architectural review and exception approval recorded in tracking tickets when external tools lack structured logger integration.
