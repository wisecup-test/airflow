---
version: 2
name: 5a73320d-cbf1-4dc6-9713-4ffb5d3d9f56-airflow-basehook-test-connection-contract-implementation-consumer-discover-ecosystem-dependency-manifest-lock
description: "Airflow BaseHook test_connection Contract Implementation: Consumer Discover Ecosystem Dependency Manifest Lock"
metadata:
  actual:
    adr_id: 5a73320d-cbf1-4dc6-9713-4ffb5d3d9f56
    status: accepted
    candidate_group_id: f8a7454a-ba33-4378-b37b-22d738843b53
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/providers/akeyless/hooks/**
---

# Airflow BaseHook test_connection Contract Implementation: Consumer Discover Ecosystem Dependency Manifest Lock

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `providers/akeyless`

Applies to:
- `src/airflow/providers/akeyless/hooks/**`

## Context

- Integration hooks communicating with external secret management vaults require a standard interface method to validate credential and host connectivity
- Base hook subclasses provide runtime connection test methods returning status tuples to indicate connectivity success or failure messages
- The observed pattern isolates credentials testing to authentication calls without triggering mutations or retrieving business payloads

## Problem Statement

External service hooks require verifiable runtime connectivity checks to confirm authentication parameters and network paths without executing state-altering operations.

## Decision

- **R-TS-001** MUST: The consumer MUST discover the ecosystem dependency manifest and lock artifact to resolve the exact locked version of all provider and client libraries before implementation

## Policy Block

In scope:

- Custom hook classes extending BaseHook interfacing with external authentication and credential services

Out of scope:

- Operator classes, sensor classes, and non-hook utility modules

## Rationale

- Implementing test_connection establishes a consistent validation protocol across service integrations
- Encapsulating authentication testing within a uniform return structure allows callers to assess endpoint reachability safely
- Isolating verification logic to authentication requests prevents unintended side effects during connectivity diagnostics

## Consequences

Positive:

- Provides uniform status reporting for connectivity verification across integration hooks
- Prevents unhandled network and credential errors from terminating diagnostic execution
- Eliminates side effects by restricting validation to identity authentication

Negative:

- Introduces runtime coupling to live external endpoints during connectivity evaluation
- Requires ongoing synchronization between connection configuration properties and authentication parameters

## Alternatives

- Performing functional payload queries to validate connectivity (rejected)
  Rejected because: Executing data retrieval requests introduces unnecessary authorization requirements and operational overhead compared to lightweight identity verification
  When valid: Valid when services lack a dedicated authentication endpoint and require payload transactions for access confirmation
- Omitting explicit connection verification methods from integration hooks (rejected)
  Rejected because: Lacks interactive diagnostic capabilities, delaying configuration error detection until pipeline runtime
  When valid: Valid for stateless integrations that do not authenticate against remote hosts

## Risks

- Authentication failures during verification may leak sensitive endpoint or credential details in failure messages
  Mitigation: Sanitize diagnostic error messages before returning them in the result tuple
  Owner: engineering team
- Network latency or rate limits on authentication endpoints can stall interactive verification requests
  Mitigation: Enforce strict timeout policies on client configuration objects used by verification routines
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
- Delegate connectivity validation directly to the underlying client authentication routine to ensure credential compatibility
- Catch general runtime exceptions at the boundary of the verification routine to construct descriptive failure responses

## Continuation Context

Verify commands:

- Discover and run the project test suite targeting hook connection validation methods
- Discover and run static type checking across provider hook modules to validate method signatures

Accept when:

- Connection testing methods return a tuple containing a boolean flag and a descriptive string message
- Authentication exceptions are captured and formatted into error messages without raising unhandled exceptions

## Enforcement

- Verified by: Automated code review and continuous integration test suites
- Violation handling: Pull requests lacking required connectivity verification methods or exception handling must be revised prior to merge
- Exception process: Document justified exceptions in architectural review when integrating with third-party systems that do not expose interactive authentication
