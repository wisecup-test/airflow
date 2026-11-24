---
version: 2
name: 6dab3756-85c8-4382-8c8c-9d3881c20e69-shared-type-definitions-via-airflow-api-fastapi-common-types-route-handlers-data-transfer-models-service
description: "Shared Type Definitions via airflow.api_fastapi.common.types: Route Handlers Data Transfer Models Service"
metadata:
  actual:
    adr_id: 6dab3756-85c8-4382-8c8c-9d3881c20e69
    status: accepted
    candidate_group_id: 88a71942-cff2-4e39-8fdb-28e65454e3e2
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/**
---

# Shared Type Definitions via airflow.api_fastapi.common.types: Route Handlers Data Transfer Models Service

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/api_fastapi/**`

## Context

- The API subsystem encompasses multiple functional domains including public endpoints, internal execution APIs, and user interface backends.
- Consistent data serialization across these surfaces requires uniform primitive types, particularly for temporal data and shared identifiers.
- A central internal module was established to provide foundational types and prevent ad-hoc type definitions across disparate routes and schemas.

## Problem Statement

Dispersed API modules often introduce divergent type aliases and inconsistent serialization behaviors for shared primitives like timestamps, leading to subtle schema mismatches and brittle cross-module integrations across separate API layers.

## Decision

- **R-LM-001** MUST: All API route handlers, data transfer models, service layers, and schema migration modules MUST import shared type primitives exclusively from airflow.api_fastapi.common.types rather than defining localized or redundant type aliases.

## Policy Block

In scope:

- All API route definitions, query parameters, and path operations within the API architecture.
- All request, response, and intermediate data models defined for API services.
- API schema version migration definitions.

Out of scope:

- Non-API core workflows and execution engine internals that operate independently of the API boundary.
- Third-party plugin extensions defining self-contained external protocols.

## Rationale

- Static analysis of the codebase demonstrates widespread adoption of airflow.api_fastapi.common.types across route controllers, datamodels, services, and version migrations.
- Centralizing type primitives ensures that serialization behaviors, such as UTC timestamp formatting, remain invariant regardless of the endpoint domain.
- A single source of truth for API primitives prevents schema drift and reduces maintenance overhead across evolving API specifications.

## Consequences

Positive:

- Guarantees consistent data serialization and schema contracts across public, execution, and user interface API endpoints.
- Eliminates duplicate type aliases and ensures centralized maintenance for core cross-cutting primitives.
- Simplifies schema versioning and API migration handling through shared baseline definitions.

Negative:

- Creates an internal dependency coupling all API layers to a single common types module.
- Modifications to foundational types require validation across the entire API test matrix.

## Alternatives

- Decentralized localized type declarations within individual domain subpackages (rejected)
  Rejected because: Leads to schema divergence, inconsistent serialization formats across endpoints, and duplicate type alias maintenance
  When valid: When domain packages are completely decoupled into distinct standalone microservices with no shared runtime contracts
- Relying directly on standard library types without domain-specific normalization (rejected)
  Rejected because: Standard library types lack API serialization constraints and uniform timezone enforcement required across endpoints
  When valid: When the application does not serialize payloads through automated schema frameworks

## Risks

- Breaking changes to a shared type alias in airflow.api_fastapi.common.types cascade across all dependent route handlers and data models.
  Mitigation: Enforce strict semantic versioning and comprehensive static analysis in CI before modifying existing exported types.
  Owner: engineering team
- Over-centralization leading to bloated shared modules containing domain-specific rather than universal primitives.
  Mitigation: Restrict common types module exclusively to ubiquitous primitives and require architectural review for additions.
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
- Locate the internal common types module within the repository structure and review all exported type aliases and classes prior to introducing new type definitions in endpoints or models.
- When extending API request schemas or query parameters with temporal fields, import UtcDateTime directly from the common types package to guarantee timezone compliance.

## Continuation Context

Verify commands:

- Discover the project static type analysis script and run it against the API packages to verify strict type conformance.
- Discover and run the project automated test suite targeting API endpoints and data model serialization.

Accept when:

- Static type analysis passes with zero type errors across all API routes, data models, and service modules.
- API test suites pass without schema serialization or deserialization failures across endpoints.

## Enforcement

- Verified by: Automated static type checkers and linters integrated into continuous integration pipelines.
- Verified by: Peer code review for all modifications affecting shared type modules and endpoint signatures.
- Violation handling: Pull requests with localized type duplication or non-compliant type aliases are blocked until refactored to use the common types module.
- Exception process: Exceptions require documented architectural approval and must demonstrate an unavoidable domain isolation requirement.
