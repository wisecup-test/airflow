---
version: 2
name: bcb12533-450e-4d30-be8e-393a114cc8aa-standardization-of-asset-contracts-on-airflow-api-fastapi-execution-api-datamodels-asset-module-route-handlers-event-response-schemas-not
description: "Standardization of Asset Contracts on airflow.api_fastapi.execution_api.datamodels.asset Module: Route Handlers Event Response Schemas Not"
metadata:
  actual:
    adr_id: bcb12533-450e-4d30-be8e-393a114cc8aa
    status: accepted
    candidate_group_id: 1b87d68b-f00a-469a-8b13-a03dc3ce8547
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Standardization of Asset Contracts on airflow.api_fastapi.execution_api.datamodels.asset Module: Route Handlers Event Response Schemas Not

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Multiple subsystems across the execution runtime require consistent representation and validation of asset entities, including REST API route handlers, serialization definitions, and runtime event schemas.
- Directly exposing or duplicating database entity attributes across public and internal runtime endpoints risks contract drift and tight coupling to persistence models.
- The codebase establishes the internal airflow.api_fastapi.execution_api.datamodels.asset module as the shared authority for asset data contracts across these distinct layers.

## Problem Statement

Inconsistent representations of asset structures across API endpoints, serialization layers, and event models lead to schema drift, serialization failures, and tight coupling between database schemas and runtime contracts.

## Decision

- **R-LM-001** MUST_NOT: API route handlers and event response schemas MUST NOT expose raw database persistence entities directly to external consumers or runtime clients.

## Policy Block

In scope:

- Execution API endpoints handling asset retrieval by name, URI, or alias.
- Serialization definition classes generating unique keys and profiles for asset entities.
- Runtime event datamodels transmitting asset state changes and associated run references.

Out of scope:

- Persistence layer database migration scripts and low-level relational table declarations.
- Unrelated execution API endpoints that do not process asset entities or asset events.

## Rationale

- Evidence shows route handlers in execution API routes directly validate persistence entities into AssetResponse instances from airflow.api_fastapi.execution_api.datamodels.asset.
- Serialization definition components rely on shared asset contract structures to construct deterministic unique keys and profile mappings.
- Asset event datamodels embed canonical asset responses to ensure composite event schemas share uniform field structures across runtime boundaries.

## Consequences

Positive:

- Guarantees schema consistency across API query endpoints, frozen serialization structures, and runtime event notifications.
- Decouples internal persistence storage models from public and runtime API response contracts.
- Provides centralized type validation and serialization logic for asset entities across core execution boundaries.

Negative:

- Changes to canonical asset datamodel fields necessitate coordinated updates across all dependent routing, serialization, and event processing components.
- Coupling between routing contracts and serialization logic introduces an internal module dependency across multiple subsystem boundaries.

## Alternatives

- Direct exposure of persistence entity models across API routes and serialization boundaries (rejected)
  Rejected because: Couples internal database schema details to external communication contracts, exposing internal attributes and risking unintended breaking changes when storage schemas evolve.
  When valid: Internal prototyping where schema isolation and contract stability guarantees are unnecessary.
- Decentralized schema declarations defined independently within each boundary layer (rejected)
  Rejected because: Introduces schema divergence and duplication across endpoints, serialization logic, and event response models, increasing maintenance overhead when entity attributes change.
  When valid: Autonomous microservices where distinct domains share no common runtime or serialization expectations.

## Risks

- Breaking changes in the shared asset schema can propagate across API routes, serialization logic, and event datamodels simultaneously.
  Mitigation: Enforce backward-compatible schema expansion policies and provide deprecation windows when altering canonical datamodel fields.
  Owner: Core Architecture Team
- Performance overhead from model validation during high-throughput execution API responses.
  Mitigation: Isolate schema validation logic from heavy runtime dependencies and enforce strict type checking across module boundaries.
  Owner: API Development Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure model validation utilizes explicit conversion methods provided by the schema classes rather than manual dictionary unpacking.
- Maintain clear boundary separation by verifying persistence models are converted to canonical datamodels before returning from execution route handlers.

## Continuation Context

Verify commands:

- Discover the repository test runner from the project manifest and execute the test suites covering execution API routes, serialization definitions, and asset event datamodels.
- Discover and run the project static analysis and type checking utilities to verify schema compliance and type integrity across all asset datamodel consumers.

Accept when:

- All execution API route handlers returning asset data validate domain records into AssetResponse models from the canonical asset datamodels module.
- Serialization definition classes and runtime event response schemas reference canonical asset data models without defining divergent duplicate schemas.
- Discovered automated test suites and static type verification checks across affected components execute without schema validation or type mismatches.

## Enforcement

- Verified by: Automated static type analysis and contract validation runs in continuous integration pipelines.
- Verified by: Mandatory peer code reviews on changes affecting execution API routes, event schemas, or serialization definitions.
- Violation handling: Automated build failure when routes or serialization modules introduce unapproved ad-hoc schemas for asset entities.
- Violation handling: Rejection of pull requests that bypass canonical asset model validation in execution boundary endpoints.
- Exception process: Submit an architectural review request outlining technical blockers that prevent direct adoption of the canonical asset datamodels module.
- Exception process: Obtain explicit sign-off from the core architecture maintainers prior to merging divergent schema definitions.
