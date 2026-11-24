---
version: 2
name: b79df375-2595-4708-bc18-9cdcc9d2556c-adoption-of-github-com-apache-airflow-go-sdk-pkg-api-client-module-callers-supply-valid-execution-context-client
description: "Adoption of github.com/apache/airflow/go-sdk/pkg/api Client Module: Callers Supply Valid Execution Context Client"
metadata:
  actual:
    adr_id: b79df375-2595-4708-bc18-9cdcc9d2556c
    status: accepted
    candidate_group_id: 525c4175-1045-421a-8fbf-e424a9e05796
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - pkg/api/mocks/**
      - sdk/**
---

# Adoption of github.com/apache/airflow/go-sdk/pkg/api Client Module: Callers Supply Valid Execution Context Client

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `bundle/bundlev1/**`
- `pkg/api/mocks/**`
- `sdk/**`

## Context

- The SDK requires structured access to external platform resources including variables, connections, dag runs, task instances, and assets.
- Without a dedicated client library layer, disparate packages would duplicate request serializations, context handling, and endpoint transport logic.
- Standardized mocking contracts are necessary to enable comprehensive unit testing of SDK workflows without requiring live backend endpoints.

## Problem Statement

Interactions with platform control plane resources require uniform serialization, request lifecycle management, and isolated mock testing. In the absence of a standardized client library, SDK components risk diverging error handling and fragmented transport layers.

## Decision

- **R-LM-001** SHOULD: Callers SHOULD supply a valid execution context to all client method invocations to guarantee cancellation and deadline propagation.

## Policy Block

In scope:

- All internal SDK packages and workload execution bundles interacting with platform control plane resources.
- Unit testing suites simulating interactions with platform endpoints.

Out of scope:

- Standalone external plugins communicating with alternate third-party endpoints outside the platform domain.
- Pure utility functions performing in-memory data conversions without network interaction.

## Rationale

- Standardizing on github.com/apache/airflow/go-sdk/pkg/api establishes a single source of truth for platform contracts and response types.
- Providing dedicated interface mocks alongside public contracts enables isolated unit testing of client consumers without live network environments.
- Separating sub-clients by domain resource adheres to interface segregation principles and prevents monolithic client bloat.

## Consequences

Positive:

- Enforces strict separation between transport-level schema handling and high-level workflow execution semantics.
- Enables deterministic, fast offline testing through standardized mock client interfaces implementing domain contracts.
- Ensures consistent context propagation, cancellation semantics, and uniform error representations across all service endpoints.

Negative:

- Introduces architectural coupling to the centralized API package schema contracts across all consumer packages.
- Requires maintenance and generation of dedicated test mock implementations whenever client interface definitions change.

## Alternatives

- Direct, inline HTTP transport calls across individual SDK utility packages (rejected)
  Rejected because: Scatters schema validation, credential management, and transport logic across disparate modules, leading to code duplication and unmaintainable testing surfaces.
  When valid: Only viable in minimal standalone single-file utilities with no shared runtime state or orchestration requirements.
- Monolithic monolithic client struct exposing untyped payloads rather than segregated domain-specific sub-clients (rejected)
  Rejected because: Violates interface segregation by forcing consumers to depend on the entirety of the service API surface rather than bounded resource contracts.
  When valid: Viable when service payloads are dynamic or unstructured without formal schema guarantees.

## Risks

- Upstream API model modifications may cause breaking changes across dependent SDK consumers.
  Mitigation: Establish automated contract compatibility tests to detect upstream schema breaks before publishing SDK updates.
  Owner: engineering team
- Mock implementations could drift from actual runtime client behavior if maintained manually.
  Mitigation: Generate mock definitions directly from interface contracts to prevent drift between production clients and test doubles.
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
- Implementations consuming client methods must propagate context instances across all synchronous and asynchronous operations to honor deadlines.
- Mock implementations must mirror all corresponding interface contract methods including parameter structs and typed response pointers.

## Continuation Context

Verify commands:

- Discover the project build and test runner from the repository manifest and execute the test suite covering client and mock implementations.
- Discover the repository static analysis tools to verify that all service communications adhere to module interface contracts.

Accept when:

- All test suites covering API client implementations and mock assertions execute cleanly without errors.
- Static analysis confirms that service interactions exclusively route through client interfaces defined in the adopted module without unabstracted transport calls.

## Enforcement

- Verified by: Automated continuous integration test suites executing mock verification tests.
- Verified by: Static architectural linting and dependency graph validation during pull request review.
- Violation handling: Pull requests introducing direct transport calls or bypassing API contracts will be rejected by automated CI checks.
- Violation handling: Violations detected in existing code must be refactored to consume the centralized client contracts prior to release tagging.
- Exception process: Exceptions require formal review by the SDK architectural review team.
- Exception process: Proposals must demonstrate why dedicated client interfaces in the adopted module cannot accommodate the proposed communication pattern.
