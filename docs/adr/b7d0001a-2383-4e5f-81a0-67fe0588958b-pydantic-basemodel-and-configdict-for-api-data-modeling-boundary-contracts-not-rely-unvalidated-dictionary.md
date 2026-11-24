---
version: 2
name: b7d0001a-2383-4e5f-81a0-67fe0588958b-pydantic-basemodel-and-configdict-for-api-data-modeling-boundary-contracts-not-rely-unvalidated-dictionary
description: "Pydantic BaseModel and ConfigDict for API Data Modeling: Boundary Contracts Not Rely Unvalidated Dictionary"
metadata:
  actual:
    adr_id: b7d0001a-2383-4e5f-81a0-67fe0588958b
    status: accepted
    candidate_group_id: 570a3ac2-93ef-4f54-a90e-5d29f549bf69
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/api/datamodels/**
---

# Pydantic BaseModel and ConfigDict for API Data Modeling: Boundary Contracts Not Rely Unvalidated Dictionary

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `src/airflowctl/api/datamodels/**`

## Context

- Client and service boundary layers must serialize and deserialize structured data across public API endpoints.
- Untrusted network inputs and authentication payloads require strict schema verification and error reporting.
- Declarative schema definitions ensure predictable field types and consistent validation rules across services.

## Problem Statement

Inconsistent data modeling and manual validation across API boundaries lead to runtime deserialization failures, unvalidated request payloads, and divergent API contract specifications.

## Decision

- **R-LM-001** MUST_NOT: API boundary contracts MUST NOT rely on unvalidated dictionary structures or raw unstructured parameter mappings.

## Policy Block

In scope:

- All public API data transfer objects, request bodies, response models, and error response schemas.

Out of scope:

- Internal low-level utility helpers and algorithms that operate exclusively on primitive structures within trusted execution contexts.

## Rationale

- Adopting Pydantic standardizes data interchange validation and serialization across authentication and domain endpoints.
- Using ConfigDict with explicit extra field controls allows sensitive endpoints to forbid unparsed parameters while allowing permissible extensions where needed.
- Annotated Field declarations ensure that API data contracts retain clear metadata and default values across generated client schemas.

## Consequences

Positive:

- Enforces strict typing and automated schema validation at system boundaries before processing request payloads.
- Provides explicit contract documentation through declarative field metadata and configurable schema rules.
- Standardizes error formats across API communication boundaries.

Negative:

- Introduces runtime instantiation and validation overhead compared to unvalidated plain dictionary access.
- Requires synchronizing model definitions with upstream API schema shifts to prevent validation rejection.

## Alternatives

- Standard library dataclasses or typed dictionaries without runtime validation (rejected)
  Rejected because: Fails to enforce runtime type coercion and strict payload validation on incoming untrusted API inputs.
  When valid: Internal-only data structures where input sources are trusted and serialization overhead must be eliminated.
- Manual dictionary inspection and bespoke validation routines (rejected)
  Rejected because: Increases code duplication, error surface area, and maintenance burden while obscuring contract definitions.
  When valid: Small scripts with no external schema dependencies and minimal boundary interactions.

## Risks

- Upstream library breaking changes between major revisions can alter model configuration semantics.
  Mitigation: Follow lock-version grounding before updating schema definitions and review release notes for configuration syntax shifts.
  Owner: engineering team
- Excessive validation complexity on large data transfer objects may introduce latency overhead.
  Mitigation: Profile deserialization paths for high-throughput endpoints and apply partial validation where appropriate.
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
- Model configurations should apply strict attribute filtering to prevent unvalidated parameters from penetrating sensitive internal logic.
- Field metadata should declare explicit titles and optional default values to facilitate schema documentation generation.

## Continuation Context

Verify commands:

- Discover and run the project static type checker to verify that all data transfer models satisfy model and field typing constraints.
- Discover and execute the repository unit test suite to ensure all serialization and deserialization validation rules pass.

Accept when:

- All data transfer models inherit from BaseModel and define model attributes using Annotated and Field metadata.
- Inbound mutation models explicitly configure forbidden undeclared attributes via ConfigDict.
- Type check and test execution passes across the data model test suites.

## Enforcement

- Verified by: Automated static type checking and schema contract validation tests executed in continuous integration.
- Verified by: Pull request peer review validating schema configurations on new or updated endpoint models.
- Violation handling: Static analysis or continuous integration build failures block merges for unvalidated models or missing lock verification.
- Violation handling: Non-compliant models must be refactored to inherit from BaseModel with explicit Field metadata.
- Exception process: Submit an architectural review request with justification detailing why standard model validation is infeasible for the specific payload boundary.
- Exception process: Obtain sign-off from API engineering leads prior to deploying unvalidated payload parsers.
