---
version: 2
name: edc469af-53ba-4c80-8f72-f11b2a624781-pydantic-basemodel-for-core-api-ui-response-data-models-nullable-optional-response-attributes-use-explicit
description: "Pydantic BaseModel for Core API UI Response Data Models: Nullable Optional Response Attributes Use Explicit"
metadata:
  actual:
    adr_id: edc469af-53ba-4c80-8f72-f11b2a624781
    status: accepted
    candidate_group_id: 645baf61-da8f-40a6-8769-d3d167629679
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/core_api/datamodels/ui/**
---

# Pydantic BaseModel for Core API UI Response Data Models: Nullable Optional Response Attributes Use Explicit

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/api_fastapi/core_api/datamodels/ui/**`

## Context

- The core API requires structured, serialized response contracts for web user interface interactions.
- Internal domain representations and scheduler entities contain operational metadata that must not be exposed directly to external interface consumers.
- Multiple API endpoints require consistent serialization rules, nested object representations, and summary collection wrappers.

## Problem Statement

Directly serializing raw domain and database models to API clients leaks internal persistence structures, couples client interfaces to internal execution schemas, and lacks automated validation for user interface responses.

## Decision

- **R-LM-001** SHOULD: Nullable or optional response attributes SHOULD use explicit union types with None and provide default values.

## Policy Block

In scope:

- API endpoints serving web user interfaces
- Response contract definitions within the API data model layer

Out of scope:

- Internal scheduler execution loops
- Database migration definitions and internal persistence entities

## Rationale

- Isolating user interface response schemas using Pydantic BaseModel ensures strict contract boundaries between internal execution state and client presentation layers.
- Declarative data models with automated serialization reduce manual dictionary formatting and prevent schema divergence across endpoint responses.
- Standardizing on Pydantic schema validation enables automated documentation generation and consistent error reporting across all UI endpoints.

## Consequences

Positive:

- Guarantees deterministic response serialization and validation for all user interface API contracts.
- Prevents internal database and scheduler execution entities from leaking into external presentation layers.
- Improves developer productivity through automated OpenAPI schema generation and static type checking.

Negative:

- Introduces serialization overhead when mapping deeply nested domain entities into response models.
- Requires maintaining parallel schema definitions when internal domain models diverge from presentation requirements.

## Alternatives

- Direct dictionary serialization of internal domain entities (rejected)
  Rejected because: Lacks compile-time and runtime type validation, causing silent contract regressions and leaking internal state.
  When valid: Prototyping lightweight internal microservices with unstable interfaces.
- Standard library dataclasses for response formatting (rejected)
  Rejected because: Does not provide built-in JSON schema generation, coercion, or deep validation capabilities required by API gateways.
  When valid: Internal computational algorithms where serialization and external validation are unnecessary.

## Risks

- Performance degradation during high-throughput serialization of large collections.
  Mitigation: Employ pagination in collection models and select lightweight projection queries to populate only required response fields.
  Owner: Engineering Team
- Schema drift between backend response models and client consumption expectations.
  Mitigation: Enforce contract testing and automated client schema generation during continuous integration.
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
- Structure response models hierarchically by decomposing reusable sub-entities into dedicated schemas and referencing them within parent responses.
- Use Field default factories for collections and nested structures to prevent shared reference bugs across model instances.

## Continuation Context

Verify commands:

- eval "$(discover_test_runner) $(discover_contract_test_suite)"
- eval "$(discover_type_checker) $(discover_datamodel_paths)"

Accept when:

- All data models defined for UI endpoints inherit from BaseModel and pass strict static type analysis.
- API contract test suites pass with zero schema validation failures or serialization errors.

## Enforcement

- Verified by: Automated contract testing and static analysis in continuous integration pipelines.
- Verified by: Mandatory peer review on pull requests modifying API response schemas.
- Violation handling: Pull requests containing untyped response structures or missing BaseModel inheritance will fail continuous integration checks and be blocked from merge.
- Exception process: Exceptions require documented justification submitted to the architecture review team and explicit approval from the API platform lead.
