---
version: 2
name: 8c62de5a-0ea5-462e-a43b-33ba4d856a41-pydantic-schema-contracts-for-public-external-api-integration-schema-contracts-specify-unexpected-property-handling
description: "Pydantic Schema Contracts for Public External API Integration: Schema Contracts Specify Unexpected Property Handling"
metadata:
  actual:
    adr_id: 8c62de5a-0ea5-462e-a43b-33ba4d856a41
    status: accepted
    candidate_group_id: 9614f4da-7437-49e3-ac2e-7c68e71a4f21
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/**
---

# Pydantic Schema Contracts for Public External API Integration: Schema Contracts Specify Unexpected Property Handling

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `docs/**`
- `src/airflowctl/**`

## Context

- The external API client integrates with remote services across network boundaries that evolve independently of client command handlers.
- Inbound and outbound payloads require rigorous structural guarantees to prevent runtime attribute errors and silent data corruption.
- Client operations must reliably serialize request parameters and deserialize server responses according to defined schemas.

## Problem Statement

Directly consuming and producing unvalidated dictionary structures across external API boundaries exposes client applications to contract drift, missing field runtime exceptions, and unpredictable serialization errors when external services evolve.

## Decision

- **R-PEA-001** MUST: Schema contracts MUST specify unexpected property handling through ConfigDict configuration, declaring whether additional attributes are explicitly accepted or forbidden.

## Policy Block

In scope:

- Public external API client request payload modeling.
- External API server response deserialization and validation.
- Client operations exchanging structured JSON contracts with external service endpoints.

Out of scope:

- Terminal formatting and display layout renderers.
- Local command line argument parsing structures.
- In-memory utility configurations not transmitted over external network protocols.

Exceptions:

- **EXC-25-001** Raw binary data streams or multipart file uploads where JSON schema contracts are inapplicable.
  Approval: API architecture maintainers
  Documentation: Document streaming endpoint behavior and custom deserialization mechanisms in module level specifications.

## Rationale

- Adopting declarative Pydantic schemas provides compile-time typing and runtime validation for all external API interactions across the client codebase.
- Explicit configuration dictionaries prevent unexpected payload attributes from silently compromising application state or causing transmission failures.
- Standardized serialization functions ensure consistent data normalization when communicating with remote endpoints.

## Consequences

Positive:

- Establishes strong runtime type guarantees and fail-fast validation for all inbound and outbound API payloads.
- Standardizes serialization behavior across diverse command workflows via uniform model serialization methods.
- Prevents contract drift and silent field corruption across decoupled client and server boundaries.

Negative:

- Introduces schema maintenance overhead whenever external API specifications or response payloads evolve.
- Imposes computational deserialization overhead for large response payloads processed at the boundary.
- Requires explicit field mapping and schema synchronization for nested payload structures.

## Alternatives

- Untyped dictionaries and primitive mappings for external API communication (rejected)
  Rejected because: Omits structural schema validation, leading to runtime KeyError exceptions and undocumented boundary data assumptions.
  When valid: Lightweight prototype scripts or exploratory network probes with no persistence or public stability requirements.
- Standard dataclasses without runtime validation or serialization configuration (rejected)
  Rejected because: Standard dataclasses do not provide automated JSON schema validation, field coercion, or declarative extra-field handling policies.
  When valid: Internal data transfer objects strictly within local boundaries where serialization and validation are handled upstream.

## Risks

- Strict contract schema validation may reject unexpected but benign response fields introduced by server updates.
  Mitigation: Configure schema extra property policies to accommodate benign backward-compatible schema enhancements from external servers.
  Owner: Engineering Team
- Manual schema maintenance can lag behind upstream API changes, causing client desynchronization.
  Mitigation: Adopt automated schema generation scripts synchronizing contracts directly with upstream OpenAPI or service specifications.
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
- Organize public contract schemas in dedicated contract modules decoupled from network client transport mechanics.
- Employ explicit model serialization parameters when translating nested schemas into outbound network request bodies.
- Ensure response error models extract structured error information while maintaining compatibility with status error handling hierarchies.

## Continuation Context

Verify commands:

- Discover and run the project static type validation commands to ensure schema contract compliance across all API client modules.
- Discover and execute the integration test suite validating serialization and deserialization against API contract models.

Accept when:

- All external API request and response data structures are validated and serialized through Pydantic contract models.
- No direct manipulation of unvalidated API response dictionaries occurs in client integration operations.
- Automated verification and static contract analysis suites pass across the integration test suite.

## Enforcement

- Verified by: Automated static contract checking and type analysis during continuous integration.
- Verified by: Peer code review on all pull requests modifying API integration client modules.
- Verified by: Integration test suites verifying payload serialization and deserialization against mock service endpoints.
- Violation handling: Pull requests containing untyped API payload access or unvalidated network dictionaries are blocked by continuous integration.
- Violation handling: Violations detected in review must be refactored into typed contract schemas prior to merge.
- Violation handling: Unvalidated boundary calls discovered in releases trigger priority remediation issues.
- Exception process: Submit an architectural request justifying why untyped payload exchange is necessary for a specific endpoint.
- Exception process: Obtain sign-off from API architecture maintainers and provide fallback boundary sanitization logic.
- Exception process: Document the exception scope and maintain an issue tracking migration to typed contract schemas.
