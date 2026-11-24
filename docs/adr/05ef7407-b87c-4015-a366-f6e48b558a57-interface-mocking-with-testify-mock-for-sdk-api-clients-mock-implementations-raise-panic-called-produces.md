---
version: 2
name: 05ef7407-b87c-4015-a366-f6e48b558a57-interface-mocking-with-testify-mock-for-sdk-api-clients-mock-implementations-raise-panic-called-produces
description: "Interface Mocking with testify/mock for SDK API Clients: Mock Implementations Raise Panic Called Produces"
metadata:
  actual:
    adr_id: 05ef7407-b87c-4015-a366-f6e48b558a57
    status: accepted
    candidate_group_id: 121e56e3-8383-4134-b341-867d020ef7cc
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/api/mocks/**
---

# Interface Mocking with testify/mock for SDK API Clients: Mock Implementations Raise Panic Called Produces

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `pkg/api/mocks/**`

## Context

- SDK consumers and internal packages require isolated unit testing capabilities without triggering live network requests to backend services.
- Client interfaces encompass multiple service domains including variables, workflow runs, task instances, assets, and connections.
- Uniform test doubles are necessary to provide predictable call expectation tracking, return value stubbing, and failure simulation across all client contracts.

## Problem Statement

Testing SDK integrations and application code against live backend endpoints creates brittle, slow, and non-deterministic test suites. To validate error handling and data flows reliably, components interacting with SDK client interfaces require a uniform, type-safe mocking pattern that supports dynamic responses and call expectation verification without managing custom state.

## Decision

- **R-TS-001** MUST: Mock implementations MUST raise a panic if Called produces an empty return slice, enforcing explicit return value definitions for every invoked method.

## Policy Block

In scope:

- Unit tests simulating SDK client interactions across service boundaries.
- Test double definitions for public client interfaces.

Out of scope:

- End-to-end integration tests requiring real network transmission against live backend services.
- Concrete HTTP transport and serialization implementations.

## Rationale

- Standardizing on testify/mock across all client boundaries provides a consistent expectation API with EXPECT, Run, and Return across distinct client domains.
- Dynamic return function dispatching via ret.Get allows tests to simulate complex response computations and context-aware behavior without altering interface contracts.
- Centralized call assertion via AssertExpectations prevents silent failures and ensures test verification rigor across disparate test suites.

## Consequences

Positive:

- Decouples client consumers and unit tests from external service availability and network latency.
- Ensures consistent mocking paradigms and assertion syntax across all SDK client components.
- Enables granular simulation of edge cases, HTTP response envelopes, and error scenarios.

Negative:

- Requires ongoing synchronization of mock implementations whenever interface signatures are updated.
- Relies on runtime reflection and type assertions within ret.Get and ret.Error, which can delay signature mismatch detection until test execution.

## Alternatives

- Manual handwritten stub structs with callback fields for each interface method (rejected)
  Rejected because: Handwritten stubs introduce significant boilerplate, inconsistent expectation tracking, and lack standardized assertion utilities across client packages.
  When valid: When external testing dependencies are strictly forbidden and interface surface area is minimal.
- Live network mocking via embedded HTTP servers or request interceptors (rejected)
  Rejected because: Network-level interception requires managing HTTP payloads, headers, and serialization in unit tests rather than operating on typed domain models.
  When valid: When verifying wire protocols, serialization schemas, and transport-level connection behaviors.

## Risks

- Mock signatures drifting from upstream interface definitions during API evolution.
  Mitigation: Enforce continuous integration checks that compile mock packages alongside interface definitions.
  Owner: SDK engineering team
- Runtime type assertion panics caused by misconfigured return types in ret.Get.
  Mitigation: Utilize structured EXPECT helpers with type-safe Return wrappers in test suites.
  Owner: SDK engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure mock structures embed the base mock instance and implement all public interface methods defined in the corresponding client contracts.
- Structure method bodies to inspect returned values using type assertions, verifying both function callbacks and static typed objects before returning.

## Continuation Context

Verify commands:

- Discover the repository test runner from project configuration and execute test targets across the client mock packages.
- Discover repository static analysis tools to verify type compatibility between mock implementations and client interfaces.

Accept when:

- All unit tests executing against client mock doubles pass expectation assertions without unhandled panics.
- Interface compilation passes confirming all mock methods satisfy client contract signatures.

## Enforcement

- Verified by: Continuous integration test pipelines executing automated unit test suites.
- Verified by: Static analysis and compiler checks verifying interface conformance.
- Violation handling: Build pipeline failure upon compilation error or unfulfilled mock assertion.
- Violation handling: Rejection of pull requests introducing untested client interfaces lacking corresponding test doubles.
- Exception process: Submit an architectural waiver detailing why an interface cannot support standard mocking structures, approved by SDK maintainers.
