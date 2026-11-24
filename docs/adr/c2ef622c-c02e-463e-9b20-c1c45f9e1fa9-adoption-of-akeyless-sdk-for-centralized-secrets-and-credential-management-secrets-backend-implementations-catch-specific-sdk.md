---
version: 2
name: c2ef622c-c02e-463e-9b20-c1c45f9e1fa9-adoption-of-akeyless-sdk-for-centralized-secrets-and-credential-management-secrets-backend-implementations-catch-specific-sdk
description: "Adoption of Akeyless SDK for Centralized Secrets and Credential Management: Secrets Backend Implementations Catch Specific Sdk"
metadata:
  actual:
    adr_id: c2ef622c-c02e-463e-9b20-c1c45f9e1fa9
    status: accepted
    candidate_group_id: 29dd61c9-798c-4bde-980c-5a872c53a794
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/providers/akeyless/**
---

# Adoption of Akeyless SDK for Centralized Secrets and Credential Management: Secrets Backend Implementations Catch Specific Sdk

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `providers/akeyless`

Applies to:
- `src/airflow/providers/akeyless/**`

## Context

- Orchestration workflows require secure access to credentials, connections, and variables without persisting sensitive material in source code or static configuration files.
- Enterprise environments utilize centralized vault platforms to enforce identity-based access control, secret rotation, and audit logging across distributed workers.
- Provider components require a unified interface to authenticate against the vault API using varied identity mechanisms including API keys, tokens, and cloud identity providers.

## Problem Statement

Managing credentials and operational configurations in distributed orchestration environments introduces risks of credential sprawl, leakage, and inconsistent rotation policies. A standardized client integration is required to interface with external vault systems across both workflow task execution and core secrets resolution backends without bespoke network protocol implementations.

## Decision

- **R-LM-001** SHOULD: Secrets backend implementations SHOULD catch specific SDK exceptions when a secret item does not exist and translate them into null returns rather than propagating unhandled exceptions.

## Policy Block

In scope:

- All provider modules interacting with the external vault platform for credentials, configuration, and variables.
- Workflow execution components requiring dynamic, rotated, or static secret retrieval.

Out of scope:

- Local development environments utilizing purely local file-based or environment-variable-based secret backends.
- Modules communicating with alternative third-party secrets management services.

## Rationale

- Adopting the official SDK encapsulates low-level protocol serialization, payload schemas, and authentication endpoints within supported interfaces.
- Sharing client construction conventions across hooks and secrets backends maintains consistency and eliminates duplicate API wrapper logic.
- Supporting multi-method authentication allows deployments to align with enterprise security baselines across heterogeneous deployment targets.

## Consequences

Positive:

- Centralizes secret storage, rotation, and access governance behind an enterprise-grade vault platform.
- Standardizes client initialization and authentication workflows across workflow hooks and secrets backend providers.
- Eliminates plain-text credentials from environment declarations and configuration artifacts through dynamic and rotated secret resolution.

Negative:

- Introduces an external client library dependency and optional authentication helper packages that must be managed and kept up to date across environments.
- Network connectivity and service availability of the external vault service become critical runtime dependencies for secret resolution and connection instantiation.
- Authentication token lifecycles require in-memory caching and renewal handling to prevent latency degradation and expired credential failures.

## Alternatives

- Direct HTTP REST integration with vault endpoints via generic HTTP clients (rejected)
  Rejected because: Bypasses official SDK serialization, authentication protocol handling, and maintenance of client-side request signatures.
  When valid: When running in severely constrained environments where external dependencies cannot be installed.
- Delegating vault communication exclusively to external command-line binaries (rejected)
  Rejected because: Introduces sub-process invocation overhead, platform binary packaging dependencies, and complex process error parsing into runtime execution paths.
  When valid: When no language-native SDK exists for the target platform.

## Risks

- Excessive authentication round-trips degrading task execution latency.
  Mitigation: Implement in-memory token caching with monotonic time-to-live expiration checks before invoking remote endpoints.
  Owner: engineering team
- Vault endpoint unavailability causing orchestrator secrets backend resolution to crash.
  Mitigation: Catch specific SDK client exceptions and return empty or fallback lookups without halting backend evaluation unexpectedly.
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
- Instantiate API clients as cached properties to reuse HTTP connection pools across repeated secret operations.
- Implement monotonic clock checks against token expiration timestamps to refresh API session tokens prior to invocation.
- Ensure optional authentication extensions are guarded with descriptive import handlers directing consumers to proper installation extras.

## Continuation Context

Verify commands:

- Run the repository test runner targeting provider secret retrieval and hook integration test suites.
- Execute the repository static analysis and linting tools across provider packages to ensure compliance with client usage rules.

Accept when:

- All provider integration tests for secret retrieval and connection resolution pass against the mock or live vault endpoint.
- Client initialization and token management verify that API tokens are cached and refreshed before expiry without unhandled authentication exceptions.
- Lock-version verification confirms that the resolved client library matches the manifest constraints and exposes all required interfaces.

## Enforcement

- Verified by: Automated static analysis verifying that direct HTTP calls are not substituted for SDK client interfaces within provider components.
- Verified by: Continuous integration test suites validating secret retrieval and backend resolution using mock API endpoints.
- Verified by: Peer code reviews ensuring adherence to token caching and error translation rules.
- Violation handling: Pull requests implementing bespoke API callers or bypassing the SDK client shall fail automated verification.
- Violation handling: Violations detected during code review must be refactored to use standard hook and backend structures before merging.
- Violation handling: Unapproved credential access patterns will trigger security review notifications.
- Exception process: Submit an architectural exception request detailing technical constraints preventing standard SDK client integration.
- Exception process: Obtain security architecture sign-off for alternative secret resolution mechanisms.
- Exception process: Document approved deviations with an explicit timeline for remediation.
