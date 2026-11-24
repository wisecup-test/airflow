---
version: 2
name: 593f5e45-606e-4018-83d6-eb05d08b00e2-axios-http-client-adoption-for-service-request-layer-service-operations-support-consumer-provided-client
description: "Axios HTTP Client Adoption for Service Request Layer: Service Operations Support Consumer Provided Client"
metadata:
  actual:
    adr_id: 593f5e45-606e-4018-83d6-eb05d08b00e2
    status: accepted
    candidate_group_id: a84f39eb-448d-4914-814e-b0e47809cb05
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Axios HTTP Client Adoption for Service Request Layer: Service Operations Support Consumer Provided Client

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Client-side service modules require reliable HTTP communication, standardized authentication injection, and structured response deserialization.
- Multiple client components define endpoint callers that invoke HTTP methods across authentication and core service boundaries.
- Transport management is consolidated by initializing client configurations and delegating request serialization, path formatting, and event stream parsing to modular core helpers.

## Problem Statement

Directly coupling API service callers to ad-hoc network request logic causes inconsistent error handling, fragmented authentication header injection, and duplicate serialization logic across client endpoints. The architecture requires a unified HTTP client library and modular helper structure that supports client override injection while isolating transport configuration from service definitions.

## Decision

- **R-LM-001** MUST: Service operations MUST support consumer-provided client overrides through request options using fallback evaluation to the default singleton client.

## Policy Block

In scope:

- Client-side service callers interacting with backend API endpoints.
- Client modules managing authentication tokens, request serialization, and event stream parsing.

Out of scope:

- Server-side service endpoints and backend router implementations.
- Static asset delivery and non-HTTP protocol communication layers.

## Rationale

- Adopting Axios inside a factory-driven client architecture centralizes transport configuration, base URL handling, and authentication header attachment across API callers.
- Providing client fallback selection in service method invocations enables consumer components to supply customized client instances for scoped contexts while maintaining a standardized default.
- Modularizing core utilities for body serialization, path formatting, and server-sent event management isolates protocol formatting concerns from endpoint method signatures.

## Consequences

Positive:

- Standardizes HTTP request dispatching across all client endpoint service definitions.
- Enables contextual client substitution through optional client arguments in request parameters.
- Separates network transport logic from request and response serialization schemas.

Negative:

- Adds indirection between service definition methods and underlying HTTP network calls.
- Requires maintaining synchronized core serialization and utility helper modules alongside client interfaces.
- Increases client bundle footprint by incorporating third-party HTTP client libraries and custom wrapper utilities.

## Alternatives

- Native Fetch API with custom service wrappers (rejected)
  Rejected because: Native fetch lacks built-in request interceptors, automatic response parsing, and unified error handling, requiring significant custom transport plumbing.
  When valid: Valid when application footprint must remain strictly minimal with zero external transport dependencies.
- Direct Axios invocation without modular serialization utilities (rejected)
  Rejected because: Coupling service endpoints directly to low-level Axios calls duplicates parameter serialization, path parsing, and auth token headers across all callers.
  When valid: Valid in simple applications with single-endpoint consumers and uniform request shapes.

## Risks

- Client version drift between dependency resolution and expected client factory options
  Mitigation: Enforce strict lock-file inspection and document verification before consuming client factory methods.
  Owner: engineering team
- Unchecked client override instances bypassing mandatory authentication or serialization interceptors
  Mitigation: Validate that custom client overrides adhere to the standardized client configuration interface.
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
- Construct client instances through the centralized client factory function to ensure consistent default configurations.
- Separate request transformations into dedicated serialization modules, keeping service definitions focused strictly on endpoint mapping.

## Continuation Context

Verify commands:

- Discover the project script runner and execute the module build check to verify client compilation.
- Discover and run the project test suite covering API client requests and serialization utilities.
- Discover and execute the static analysis linter to confirm adherence to client instantiation rules.

Accept when:

- Client instantiation compiles without type errors and routes network traffic through configured client instances.
- All service calls fall back to the default client when an override client is omitted in options.
- Project test suites and static analysis checks pass with zero violations.

## Enforcement

- Verified by: Automated static analysis rules verifying that service callers do not bypass the client factory.
- Verified by: Peer code review ensuring new endpoint definitions utilize standard client parameter patterns.
- Violation handling: Pull requests containing direct unconfigured network calls or missing client fallback options are blocked in review.
- Violation handling: Non-compliant service definitions must be refactored to use the client factory and modular utility structure.
- Exception process: Exceptions for specialized network communication must be submitted with architectural rationale and reviewed by the lead maintainers.
