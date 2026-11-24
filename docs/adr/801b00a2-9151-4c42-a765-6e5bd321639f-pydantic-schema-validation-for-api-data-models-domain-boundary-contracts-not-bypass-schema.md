---
version: 2
name: 801b00a2-9151-4c42-a765-6e5bd321639f-pydantic-schema-validation-for-api-data-models-domain-boundary-contracts-not-bypass-schema
description: "Pydantic Schema Validation for API Data Models: Domain Boundary Contracts Not Bypass Schema"
metadata:
  actual:
    adr_id: 801b00a2-9151-4c42-a765-6e5bd321639f
    status: accepted
    candidate_group_id: a577c1aa-7504-47e2-9d05-56f68d2a89a1
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/api/datamodels/**
---

# Pydantic Schema Validation for API Data Models: Domain Boundary Contracts Not Bypass Schema

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `src/airflowctl/api/datamodels/**`

## Context

- Data contracts exchanged across network boundaries require consistent validation, serialization, and deserialization semantics.
- Unvalidated input payloads risk injecting extraneous attributes and causing downstream contract drift.
- Public contracts must enforce strict attribute constraints on mutation requests while accommodating extensible response structures where necessary.

## Problem Statement

Network data exchange requires reliable type validation and protection against malformed or extraneous input attributes, but untyped dictionaries and unstructured objects permit silent schema divergence and security vulnerabilities.

## Decision

- **R-BRE-001** MUST_NOT: Domain boundary contracts MUST NOT bypass schema validation by transmitting untyped dictionaries or raw generic mappings for structured payloads.

## Policy Block

In scope:

- Public API request data models, ingress mutation payloads, and domain boundary response serializers.

Out of scope:

- Internal domain entities and purely procedural utility functions operating entirely within trusted execution boundaries.

## Rationale

- Adopting Pydantic BaseModel establishes a single declarative source of truth for domain boundary schemas and data serialization.
- Configuring strict extra field handling prevents parameter injection and unrecognized attribute processing in mutation payloads.
- Annotated Field metadata guarantees clear contract titling and boundary constraint documentation across all API contracts.

## Consequences

Positive:

- Prevents payload pollution and mass assignment vulnerabilities on ingress endpoints.
- Standardizes error reporting structure across validation failures.
- Provides explicit schema documentation and type enforcement for data consumers.

Negative:

- Runtime model instantiation incurs parsing overhead compared to raw dictionary access.
- Schema generation requires strict synchronization with upstream service contract updates.

## Alternatives

- Plain language data structures with custom manual validation functions (rejected)
  Rejected because: Manual validation functions duplicate boilerplate, introduce inconsistent boundary checks, and lack standardized serialization metadata.
  When valid: When runtime performance requires zero-dependency lightweight data containers without schema enforcement.
- Standard library dataclasses without input validation constraints (rejected)
  Rejected because: Dataclasses do not natively perform runtime input parsing, coercion, or strict extraneous attribute rejection.
  When valid: When internal state objects never receive untrusted network inputs and require no serialization schema.

## Risks

- Serialization parsing latency on high-throughput data pipelines.
  Mitigation: Establish automated regression test suites verifying performance benchmarks on high-throughput data paths.
  Owner: engineering team
- Incompatible upstream schema changes causing unexpected validation rejections.
  Mitigation: Generate contract definitions from centralized API specifications and enforce CI schema verification checks.
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
- Construct request schemas using explicit type hints combined with Annotated Field descriptors to convey schema constraints.
- Ensure error response serialization models mirror the standardized validation error structure defined for domain endpoints.

## Continuation Context

Verify commands:

- Discover and execute the repository's test runner to verify data model validation behavior against valid and invalid payloads.
- Discover and execute the project's static type analysis suite to verify type annotations across all data model definitions.

Accept when:

- All data model contracts inherit from Pydantic BaseModel and pass schema validation tests.
- Request body models enforce strict extra field rejection through configuration.
- Static analysis verifies complete type annotation and Field metadata coverage across all model definitions.

## Enforcement

- Verified by: Automated test suites executing schema validation assertions against valid and invalid payloads.
- Verified by: Static type checking and linting rules enforced within the continuous integration pipeline.
- Verified by: Architecture and code review inspection of data model definitions.
- Violation handling: Continuous integration checks fail when models bypass BaseModel inheritance or omit strict configuration on ingress schemas.
- Violation handling: Pull requests introducing unvalidated data structures are blocked until brought into compliance.
- Exception process: Submit an architectural review request documenting why dynamic or unvalidated payload structures are required.
- Exception process: Require sign-off from domain leads before permitting unvalidated input models.
