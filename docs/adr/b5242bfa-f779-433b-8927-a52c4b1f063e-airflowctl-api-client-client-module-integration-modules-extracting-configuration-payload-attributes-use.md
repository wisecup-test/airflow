---
version: 2
name: b5242bfa-f779-433b-8927-a52c4b1f063e-airflowctl-api-client-client-module-integration-modules-extracting-configuration-payload-attributes-use
description: "airflowctl.api.client Client Module Integration: Modules Extracting Configuration Payload Attributes Use"
metadata:
  actual:
    adr_id: b5242bfa-f779-433b-8927-a52c4b1f063e
    status: accepted
    candidate_group_id: 5e00389a-d966-4155-8b68-86523b1752d8
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# airflowctl.api.client Client Module Integration: Modules Extracting Configuration Payload Attributes Use

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `src/airflowctl/**`

## Context

- Static analysis detected integration access patterns across command execution, console formatting, and client communication modules.
- The codebase relies on airflowctl.api.client to coordinate HTTP communication between the command-line interface and the server API.
- Runtime dictionary lookups and client method calls were captured under the integration testing facet despite existing within production runtime modules.
- The codebase lacks an explicit integration test harness within the observed evidence, leaving client-server interactions verified only via manual CLI invocation or unobserved test suites.

## Problem Statement

Production command-line utilities and API operations require consistent integration boundaries with the backend server; however, the absence of explicit, verified integration test harnesses in the observed modules risks undetectable contract drift between CLI client requests and API endpoints.

## Decision

- **R-TS-001** SHOULD: Modules extracting configuration or payload attributes SHOULD use defensive retrieval methods with fallback defaults to prevent runtime key errors during contract divergence.

## Policy Block

In scope:

- Client-side communication modules interfacing with backend API services
- CLI command modules handling external configuration, credentials, and payload transformation

Out of scope:

- Local standalone helper utilities that do not transmit data over network boundaries
- Static console formatting functions that operate purely on in-memory tabular structures

## Rationale

- Centralizing HTTP requests within airflowctl.api.client ensures consistent retry policies, error handling, and header propagation across all CLI commands.
- Defensive attribute extraction isolates command execution from unexpected changes in API payload shapes, minimizing runtime crashes.
- Formalizing integration testing expectations prevents silent API contract breakage between client commands and backend service endpoints.

## Consequences

Positive:

- Unified error handling and authentication token injection across all CLI command modules.
- Resilience against unexpected or missing attributes in API responses through defensive data access.
- Clear boundary separation between command execution logic and underlying network protocols.

Negative:

- Additional boilerplate required when introducing new endpoints or command options.
- Heuristic detection may misidentify defensive runtime dictionary access as test assertions without explicit test markers.

## Alternatives

- Direct unmanaged HTTP calls from individual CLI command handlers (rejected)
  Rejected because: Scattering raw HTTP calls duplicates authentication, retry, and error normalization logic across multiple modules.
  When valid: One-off diagnostic scripts that operate outside the core CLI architecture.
- Strict schema validation without defensive key fallbacks (rejected)
  Rejected because: Strict schema failure crashes the CLI on optional server payload attributes during cross-version operations.
  When valid: Strictly version-locked client-server deployments where contract divergence is impossible.

## Risks

- Silent masking of missing required attributes due to permissive fallback defaults in data extraction.
  Mitigation: Enforce required parameter validation at command boundaries before calling defensive retrieval functions.
  Owner: CLI Engineering Team
- Drift between client-side data models and server API response schemas in the absence of end-to-end integration tests.
  Mitigation: Implement contract verification in automated test workflows comparing client models against API schemas.
  Owner: Quality Assurance Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Locate the primary client abstraction module within the project source tree to inspect supported authentication mechanisms and retry decorators.
- Identify existing test suites and test configuration files to add integration test scenarios validating client operations against mock server endpoints.

## Continuation Context

Verify commands:

- Discover and run the project static analysis and linting suite from repository configuration files.
- Discover and execute the automated test runner targeting integration and client unit test specifications.

Accept when:

- All client operations route through the centralized API client module without direct unmanaged HTTP requests.
- Automated test suites pass without unhandled client connection or response deserialization errors.

## Enforcement

- Verified by: Automated continuous integration pipeline executing static analysis and test runner suites.
- Verified by: Peer review verifying adherence to centralized client calling conventions and error handling patterns.
- Violation handling: Merge requests containing raw HTTP calls or unhandled network exceptions will be blocked pending refactoring.
- Exception process: Exceptions for diagnostic or emergency bypass commands require approval from the lead architect and must be documented in code comments.
