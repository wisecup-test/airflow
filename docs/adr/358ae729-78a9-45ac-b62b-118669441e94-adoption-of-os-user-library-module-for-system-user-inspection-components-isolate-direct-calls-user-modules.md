---
version: 2
name: 358ae729-78a9-45ac-b62b-118669441e94-adoption-of-os-user-library-module-for-system-user-inspection-components-isolate-direct-calls-user-modules
description: "Adoption of os/user Library Module for System User Inspection: Components Isolate Direct Calls User Modules"
metadata:
  actual:
    adr_id: 358ae729-78a9-45ac-b62b-118669441e94
    status: accepted
    candidate_group_id: f8fd4360-092d-497b-a7b0-1749904dde52
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/api/**
      - pkg/worker/**
---

# Adoption of os/user Library Module for System User Inspection: Components Isolate Direct Calls User Modules

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `pkg/api/**`
- `pkg/worker/**`

## Context

- Multiple subsystem initialization routines require host user identity and operating system context during startup.
- The os/user module provides standardized mechanisms to query current user credentials and identity attributes.
- Initialization sequences record error states using err.Error() when operating system interactions fail.

## Problem Statement

Subsystems that interact with the host platform require reliable identification of the executing user context during startup, but inconsistent access to host user metadata or unhandled operating system inspection failures can lead to misconfigured execution states and startup crashes.

## Decision

- **R-LM-001** MUST: Components MUST isolate direct calls to os and os/user modules within initialization boundaries rather than scattering user identity resolution across downstream business logic.

## Policy Block

In scope:

- Subsystem initialization routines requiring host user identification and execution context.
- Startup procedures performing platform environment validation.

Out of scope:

- Stateless application logic that does not interface with host user permissions or operating system identity.
- Downstream request-handling routines where user context has already been established at initialization.

## Rationale

- Import evidence across initialization routines demonstrates consistent adoption of os/user for user lookup alongside os for system interaction.
- Standardizing on os/user avoids non-portable process execution and guarantees consistent user attribute resolution across target deployment environments.
- Capturing error output via err.Error() during initialization ensures operational visibility when host user queries fail.

## Consequences

Positive:

- Standardizes operating system user inspection across independent initialization lifecycles.
- Provides structured access to user identity without relying on external shell execution.
- Improves startup diagnostics through uniform error string formatting.

Negative:

- Introduces an early runtime failure mode if host operating system user lookup APIs fail or access is restricted.
- Couples startup routines to host platform identity availability in minimal container environments.

## Alternatives

- Ad-hoc shell command execution for user identity extraction (rejected)
  Rejected because: Spawning external processes introduces security risks, platform portability inconsistencies, and process execution overhead.
  When valid: Valid only in legacy execution environments lacking native operating system user lookup bindings.
- Relying exclusively on ambient environment variables without os/user inspection (rejected)
  Rejected because: Environment variables can be unpopulated, spoofed, or inconsistent across different deployment hosts.
  When valid: Valid in isolated test sandboxes where host identity is mocked.

## Risks

- User identity resolution failures under restricted environments or minimal containers without user databases.
  Mitigation: Validate error states using err.Error() handling and ensure fallback or clear diagnostics on failure.
  Owner: Engineering Team
- Performance latency during user lookup operations if directory services are engaged.
  Mitigation: Confine os/user lookups strictly to initialization phases rather than per-request execution paths.
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
- Invoke os/user resolution routines within subsystem initialization procedures to cache user credentials prior to handling operational traffic.
- Format and record all initialization error messages using err.Error() to ensure diagnostic traceability.

## Continuation Context

Verify commands:

- Discover and execute the repository test suite to verify subsystem initialization completes without user lookup failures.
- Discover and execute the repository static analysis and linting checks to ensure os/user invocations adhere to error handling requirements.

Accept when:

- All initialization unit and integration tests pass without unhandled user lookup exceptions.
- Static analysis reports zero unhandled errors from os and os/user module calls in initialization routines.

## Enforcement

- Verified by: Continuous integration test execution and automated static analysis checks.
- Verified by: Peer code review verifying os/user usage is restricted to initialization routines with appropriate error handling.
- Violation handling: Build pipeline failure on unhandled errors from system calls.
- Violation handling: Code review rejection for direct user lookup invocations outside designated initialization boundaries.
- Exception process: Documented architecture exception request reviewed and approved by the engineering architecture team.
