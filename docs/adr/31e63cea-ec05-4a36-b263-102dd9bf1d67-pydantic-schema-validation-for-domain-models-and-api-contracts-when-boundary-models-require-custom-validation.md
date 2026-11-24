---
version: 2
name: 31e63cea-ec05-4a36-b263-102dd9bf1d67-pydantic-schema-validation-for-domain-models-and-api-contracts-when-boundary-models-require-custom-validation
description: "Pydantic Schema Validation for Domain Models and API Contracts: When Boundary Models Require Custom Validation"
metadata:
  actual:
    adr_id: 31e63cea-ec05-4a36-b263-102dd9bf1d67
    status: accepted
    candidate_group_id: 49981b0c-6c01-4866-af66-e6deccdf65a3
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Pydantic Schema Validation for Domain Models and API Contracts: When Boundary Models Require Custom Validation

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Distributed service boundaries and HTTP endpoints require robust runtime validation to ensure incoming and outgoing data conforms to agreed domain contracts.
- Directly exposing internal persistence entities to external consumers couples the database schema to client contracts and risks unintentional information disclosure.
- Consistent serialization and deserialization logic across runtime execution boundaries reduces defect rates and standardizes validation failure handling.

## Problem Statement

Without unified declarative schema definitions, boundary data transfer relies on disparate parsing routines and exposes internal persistence representations, increasing defect vulnerability and breaking change risks.

## Decision

- **R-BRE-001** MUST: When boundary models require custom validation, type coercion, or attribute transformation, implementations MUST use declarative Pydantic mechanisms such as field_validator, model_validator, BeforeValidator, or Field aliases.

## Policy Block

In scope:

- Definition of public and internal HTTP API request bodies, query parameters, and response structures
- Definition of inter-process serialization payloads and workload dispatch schemas across runtime execution boundaries
- Validation and transformation of untrusted inputs into structured domain transfer objects

Out of scope:

- Internal database persistence models and database migration definitions
- Low-level binary protocol streams or performance-critical raw byte buffers that bypass serialization layers

## Rationale

- Adopting Pydantic standardizes declarative validation and predictable serialization across all public and internal boundary interfaces.
- Dedicated boundary models protect internal persistence representations from accidental leakage and enable seamless API versioning.
- Declarative validation decorators eliminate boilerplate validation functions and centralize schema constraints within the domain models.

## Consequences

Positive:

- Enforces strict runtime type validation and schema consistency across all external and inter-process boundaries.
- Decouples public API and workload contracts from internal persistence layers, permitting independent model evolution.
- Provides automated contract introspection and schema generation without duplicate definitions.

Negative:

- Schema validation and object instantiation introduce measurable runtime overhead compared to unvalidated data structures.
- Engineers must maintain dedicated transfer schemas alongside internal domain models, introducing model mapping overhead.

## Alternatives

- Ad-hoc manual dictionary validation and custom sanitization functions (rejected)
  Rejected because: Manual parsing lacks centralized contract documentation, increases boilerplate, and introduces inconsistent error handling across endpoints.
  When valid: Permissible only in standalone throwaway utility scripts that do not expose external contracts.
- Direct exposure of internal database persistence entities across boundary interfaces (rejected)
  Rejected because: Tightly couples external interfaces to internal storage schemas, risking unintended data leakage and complicating database migrations.
  When valid: Permissible during initial exploratory prototyping before stabilizing production contracts.

## Risks

- Serialization and validation latency on high-frequency inter-process execution paths may impact throughput.
  Mitigation: Profile critical execution paths and utilize streamlined schema definitions with minimal validator chains on hot messaging loops.
  Owner: Core engineering team
- Inconsistent validator conventions or behavioral differences between minor library versions across environments.
  Mitigation: Strictly enforce lock-version grounding before utilizing newer validator conventions or configuration syntax.
  Owner: Architecture governance team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Identify existing shared base schema classes within the project to inherit established configuration conventions and alias mapping policies.
- When evolving legacy schemas, provide default optional values for newly introduced fields to preserve backward compatibility during deserialization.

## Continuation Context

Verify commands:

- Discover and execute the repository test runner targeting schema validation and serialization suites.
- Discover and execute the repository static analysis tools to verify model type annotations and validator decorator signatures.

Accept when:

- All automated schema validation tests and boundary contract test suites execute successfully.
- Static type verification and lint analysis report zero errors across all model definitions and validator implementations.

## Enforcement

- Verified by: Continuous integration pipelines verifying serialization and deserialization test suites
- Verified by: Static analysis linters enforcing model typing and validator method signatures
- Verified by: Peer code reviews validating that persistence entities are decoupled from boundary schemas
- Violation handling: Pull requests containing unvalidated boundary dictionaries or directly exposed persistence models are blocked by review gates
- Violation handling: Validation failures detected during automated testing halt build promotion until addressed
- Exception process: Architectural exceptions permitting unvalidated payloads or raw dictionary propagation must be submitted via formal architectural review with explicit rationale.
