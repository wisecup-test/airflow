---
version: 2
name: 8f4188df-67c4-4e0e-8778-70bc4b29ac54-akeyless-sdk-client-integration-secret-retrieval-operations-use-typed-request
description: "Akeyless SDK Client Integration: Secret Retrieval Operations Use Typed Request"
metadata:
  actual:
    adr_id: 8f4188df-67c4-4e0e-8778-70bc4b29ac54
    status: accepted
    candidate_group_id: d4289d50-4e37-4e1c-9bde-4de6e4bca425
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/**
---

# Akeyless SDK Client Integration: Secret Retrieval Operations Use Typed Request

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `providers/akeyless`

Applies to:
- `src/airflow/**`

## Context

- External secret management requires standardized access patterns across workflow tasks and orchestrator configuration lookups.
- Direct ad-hoc HTTP interactions with secrets vaults introduce duplicate connection management, fragmented authentication mechanisms, and uncoordinated token lifecycles.
- Centralizing external secrets platform interactions through dedicated hook and backend abstractions isolates API protocol details from downstream task execution and configuration resolution.

## Problem Statement

Workflow orchestration requires resilient, secure retrieval of credentials and configuration items from an external vault service without exposing raw API protocol complexities, duplicating authentication flows, or leaking client session lifecycles into task logic.

## Decision

- **R-PEA-001** MUST: Secret retrieval operations MUST use typed request objects such as GetSecretValue and parse the returned payload through normalized extraction methods.

## Policy Block

In scope:

- Integration components and backend providers interacting with the Akeyless Vault Platform for secret and variable retrieval.
- Connection management routines mediating external vault authentication and session caching.

Out of scope:

- Internal task-level data structures that do not communicate with external secret management services.
- Secret backends and connection hooks targeting alternate vault providers.

## Rationale

- Encapsulating the akeyless SDK behind dedicated hook and backend classes establishes a single point of enforcement for authentication protocol dispatch and API client configuration.
- Token caching within backend access paths reduces authentication round-trips to the external service, avoiding API rate limiting and latency overhead during configuration lookups.
- Standardizing on typed SDK request objects ensures uniform error handling and response extraction across diverse secret types.

## Consequences

Positive:

- Decouples task execution logic from external vault protocol details and API request structures.
- Provides unified authentication handling across multiple credential strategies while preserving credential isolation.
- Reduces network latency and endpoint load via centralized token caching and client lifecycle management.

Negative:

- Adds an abstraction layer over the SDK that must be maintained as upstream API features evolve.
- Requires credential configuration parameters to be structured according to provider conventions.

## Alternatives

- Direct REST API Integration via HTTP Client (rejected)
  Rejected because: Requires custom payload serialization, manual authentication header management, and bespoke error handling across all calling modules.
  When valid: Valid when operating in constrained environments where external SDK dependencies cannot be packaged.
- Ad-hoc SDK Instantiation in Task Callables (rejected)
  Rejected because: Duplicates client configuration and authentication logic while preventing connection pooling and token reuse.
  When valid: Valid only in standalone single-use scripts that operate outside the orchestration framework.

## Risks

- Authentication token expiration during prolonged execution sessions causing request failure.
  Mitigation: Implement monotonic clock comparisons against configured token time-to-live thresholds to refresh tokens proactively.
  Owner: engineering team
- External service availability interruptions blocking orchestrator configuration resolution.
  Mitigation: Handle service exceptions gracefully in backend lookups and allow fallback to secondary configuration sources.
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
- Ensure cached client instances use lazy property initialization so configuration attributes are resolved only when an active connection is initiated.
- Structure secret retrieval routines to catch API exception classes and return empty or default values where missing configuration is permissible.

## Continuation Context

Verify commands:

- Discover the repository test runner from the project manifest and execute the test suite covering vault hook and secret backend modules.
- Discover the static analysis tool configured in the repository and run type and style checks across the provider integration code.

Accept when:

- All unit and integration tests covering vault client authentication and secret retrieval execute successfully without errors.
- Static type analysis passes with zero type errors across the provider hook and backend modules.

## Enforcement

- Verified by: Continuous integration test execution on pull requests affecting provider integration components.
- Verified by: Automated static analysis and code review verification of hook and backend abstractions.
- Violation handling: Pull requests introducing direct HTTP vault calls or unencapsulated client creation will be blocked during code review.
- Violation handling: Build failures triggered by missing lock version verification or failing integration tests block merging.
- Exception process: Exceptions require approval from architectural maintainers with documented justification for custom protocol access.
