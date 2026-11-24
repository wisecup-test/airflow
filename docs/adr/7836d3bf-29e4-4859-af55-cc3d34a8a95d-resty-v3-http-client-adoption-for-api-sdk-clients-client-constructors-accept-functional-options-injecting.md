---
version: 2
name: 7836d3bf-29e4-4859-af55-cc3d34a8a95d-resty-v3-http-client-adoption-for-api-sdk-clients-client-constructors-accept-functional-options-injecting
description: "Resty v3 HTTP Client Adoption for API SDK Clients: Client Constructors Accept Functional Options Injecting"
metadata:
  actual:
    adr_id: 7836d3bf-29e4-4859-af55-cc3d34a8a95d
    status: accepted
    candidate_group_id: 9296ec27-893e-4f9f-89aa-328568a4ded1
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/api/**
---

# Resty v3 HTTP Client Adoption for API SDK Clients: Client Constructors Accept Functional Options Injecting

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `pkg/api/**`

## Context

- The SDK requires an HTTP client transport layer capable of managing base URLs, authorization headers, request middleware, and debug logging across multiple service sub-clients.
- API client interfaces require both high-level typed domain object retrieval and low-level access to HTTP status codes, headers, and raw responses.
- Consistent client instantiation, option configuration, and testing mock generation are necessary across all domain-specific API clients in the module.

## Problem Statement

Without a standardized HTTP client library and shared interface convention, SDK API sub-clients risk divergent transport configurations, inconsistent middleware pipelines, repetitive header handling, and disparate test mocking strategies.

## Decision

- **R-LM-001** SHOULD: Client constructors SHOULD accept functional options for injecting custom client transports and request middleware chains.

## Policy Block

In scope:

- All HTTP client implementations, functional client options, and client middleware within the API SDK.
- All mock interfaces and test doubles representing API service sub-clients.

Out of scope:

- Internal non-HTTP communication protocols or in-process direct function calls.
- Command-line utilities and tools that do not interact with the backend API service.

## Rationale

- Adopting Resty v3 provides built-in request middleware, base URL management, header cloning, and debugging capabilities, eliminating redundant boilerplate across client modules.
- Standardizing client signatures to expose both typed payloads and raw responses satisfies consumers needing entity models as well as those needing low-level response metadata inspection.
- Consistent client construction via functional options enables flexible composition of request middleware and custom transport configuration.

## Consequences

Positive:

- Provides centralized request middleware, logging, and connection management across all API sub-clients.
- Exposes consistent dual-return contracts enabling both typed entity deserialization and raw HTTP response inspection.
- Simplifies client cloning and authorization header propagation via standardized client initialization methods.
- Enables deterministic interface mocking across all client service boundaries in unit and integration test suites.

Negative:

- Sub-client interface contracts are coupled to third-party response types, requiring breaking changes if the underlying HTTP library is altered.
- Careful client cloning is required when applying per-request or per-token configuration to avoid mutating shared client instances.
- Direct header manipulation may be necessary to bypass default security warnings when operating under specific token schemes.

## Alternatives

- Standard library HTTP client without external wrapper dependencies (rejected)
  Rejected because: Requires manual re-implementation of request middleware, header propagation, base URL routing, and structured logging capabilities across every client module.
  When valid: Valid when building zero-dependency minimal libraries where third-party dependency footprints must be completely eliminated.
- Alternative third-party HTTP client frameworks (rejected)
  Rejected because: Introduces divergent client configuration patterns, conflicting middleware interfaces, and inconsistent response models across SDK sub-clients.
  When valid: Valid when an existing enterprise framework mandates a distinct client abstraction across all repository components.

## Risks

- Breaking API changes in future major library versions could necessitate widespread updates across client interfaces.
  Mitigation: Encapsulate client initialization within factory constructors and provide functional options to insulate external consumers from transport details.
  Owner: engineering team
- Improper client cloning when modifying authorization headers could result in shared state contamination across client instances.
  Mitigation: Enforce that client duplication copies header maps and options defensively before applying instance-specific configuration.
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
- Client options such as request middleware and underlying HTTP clients should be passed into client constructors as functional options.
- When deriving authenticated clients with bearer tokens, duplicate client configuration and copy existing header maps to preserve immutability across client instances.
- Ensure mock generators replicate both typed entity methods and response-returning method contracts across all client sub-interfaces.

## Continuation Context

Verify commands:

- Discover the project repository build definition and execute the primary package compilation command to confirm all client interfaces and mock definitions compile cleanly.
- Discover and execute the repository unit and integration test suite to verify client mock expectations and HTTP transport contract adherence.

Accept when:

- Client constructors configure base URLs, middleware chains, and headers through the designated HTTP client abstraction.
- Service client contracts and test mocks expose uniform dual-method signatures returning domain responses alongside the standardized HTTP client response type.
- Verification directives executed across the repository demonstrate zero compilation errors or unmocked client contract mismatches.

## Enforcement

- Verified by: Automated static analysis and module dependency checks in the continuous integration pipeline.
- Verified by: Interface compliance checks validating that client mocks implement standardized dual-response signatures.
- Verified by: Mandatory peer review on all additions or modifications to API client packages.
- Violation handling: Pull requests introducing unapproved HTTP transport libraries or bypassing client option patterns will be blocked by automated checks.
- Violation handling: Incompatible client interface signatures failing mock assertions must be refactored prior to merge.
- Exception process: Submit an architectural change request detailing why the standard HTTP client library cannot support the specific protocol requirements.
- Exception process: Obtain explicit approval from the architectural review team prior to introducing divergent transport implementations.
- Exception process: Document the exception and isolate any non-standard transport within an internal adapter package.
