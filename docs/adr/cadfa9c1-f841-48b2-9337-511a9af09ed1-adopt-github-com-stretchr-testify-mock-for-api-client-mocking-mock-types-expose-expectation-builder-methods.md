---
version: 2
name: cadfa9c1-f841-48b2-9337-511a9af09ed1-adopt-github-com-stretchr-testify-mock-for-api-client-mocking-mock-types-expose-expectation-builder-methods
description: "Adopt github.com/stretchr/testify/mock for API Client Mocking: Mock Types Expose Expectation Builder Methods"
metadata:
  actual:
    adr_id: cadfa9c1-f841-48b2-9337-511a9af09ed1
    status: accepted
    candidate_group_id: af046700-1fff-4407-a1d4-fe196133d531
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/api/mocks/**
---

# Adopt github.com/stretchr/testify/mock for API Client Mocking: Mock Types Expose Expectation Builder Methods

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `pkg/api/mocks/**`

## Context

- The client SDK defines interface boundaries for API operations requiring deterministic test isolation without remote network calls.
- Downstream consumer tests require reliable test doubles that simulate successful and erroneous API responses.
- Multiple client mock implementations adopt github.com/stretchr/testify/mock to implement method interception and assertion tracking.

## Problem Statement

Testing client SDK consumers against live service backends introduces network latency, environmental coupling, and non-deterministic test failures. Without a standardized mocking convention, disparate test doubles risk diverging from API contracts and lack uniform assertion verification for method invocations, parameter matching, and error simulation.

## Decision

- **R-LM-001** SHOULD: Mock types SHOULD expose expectation builder methods supporting EXPECT, Run, and Return to configure invocation assertions.

## Policy Block

In scope:

- Unit and consumer test suites defining or using test doubles for API client interfaces.
- Mock client implementations within client SDK packages.

Out of scope:

- Production client runtime code communicating with external service backends.
- End-to-end integration tests configured to run against real backend services.

Exceptions:

- **EXC-20-001** Integration tests require network-level transport stubbing rather than interface-level mock doubles.
  Approval: SDK Maintainers
  Documentation: Document rationale and mock server configuration within the integration test package documentation.

## Rationale

- The codebase shows consistent adoption of github.com/stretchr/testify/mock across API client mock definitions.
- Method interception through Called, Get, and Error standardizes return payload assignment and typed error simulation.
- Expectation verification through AssertExpectations ensures test doubles validate expected call frequencies and parameter values.

## Consequences

Positive:

- Standardizes test double creation across all API client interfaces.
- Enables deterministic call count, parameter value, and error injection verification without network overhead.
- Provides structured builder patterns for configuring return values and mock behaviors.

Negative:

- Relies on reflection and runtime type assertions inside mock method dispatchers.
- Requires maintaining mock method definitions whenever client interface signatures change.

## Alternatives

- Hand-rolled struct-based mock implementations with function fields (rejected)
  Rejected because: Manual mock structures introduce boilerplate across client packages, lack built-in call count assertions, and increase the risk of silent verification omission.
  When valid: Trivial single-method interface doubles where assertion verification is unnecessary.
- In-memory mock HTTP transport servers returning static responses (rejected)
  Rejected because: Higher execution overhead and complex request routing setup for isolated unit test scenarios where interface-level mocking is sufficient.
  When valid: Validating wire-level HTTP serialization, header processing, and transport protocol behavior.

## Risks

- Mock implementations diverging from evolving client interface definitions.
  Mitigation: Enforce compile-time interface implementation checks for all mock structures to detect contract drift immediately.
  Owner: Engineering Team
- Runtime panics triggered by unexpected method calls without preconfigured return values.
  Mitigation: Structure mock methods with defensive return extraction and configure explicit expectations in test setups.
  Owner: SDK Maintainers

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Declare compile-time interface satisfaction checks ensuring mock structs implement their corresponding client interfaces.
- Ensure return value extraction handles dynamic return functions as well as static type assertions defensively.

## Continuation Context

Verify commands:

- Discover the test runner invocation script from the project configuration and execute the unit test suite targeting mock package implementations.
- Execute the project verification target to evaluate mock contract conformity and assertion validation.

Accept when:

- All client test suites executing mock expectations pass with zero unhandled panics or assertion failures.
- Mock implementations successfully satisfy their corresponding client contract interfaces without interface drift.

## Enforcement

- Verified by: Automated continuous integration test runs executing unit test suites.
- Verified by: Static analysis and interface compliance assertions during compilation.
- Verified by: Peer review of pull requests introducing or modifying API client contracts.
- Violation handling: Test failure in continuous integration if mock assertions or expectations fail.
- Violation handling: Rejection of pull requests introducing hand-rolled or unstructured mock doubles for public client interfaces.
- Exception process: Submit an architecture review request documenting why interface mocking with the adopted library does not satisfy the testing requirements.
