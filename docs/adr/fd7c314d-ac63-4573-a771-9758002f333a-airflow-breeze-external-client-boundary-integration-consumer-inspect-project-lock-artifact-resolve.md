---
version: 2
name: fd7c314d-ac63-4573-a771-9758002f333a-airflow-breeze-external-client-boundary-integration-consumer-inspect-project-lock-artifact-resolve
description: "airflow_breeze External Client Boundary Integration: Consumer Inspect Project Lock Artifact Resolve"
metadata:
  actual:
    adr_id: fd7c314d-ac63-4573-a771-9758002f333a
    status: accepted
    candidate_group_id: a27b0bc4-eeae-4ed7-841f-347fc5e9dfd7
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
      - backport/**
      - breeze/src/airflow_breeze/**
      - ide_setup/**
      - registry/**
      - send_email.py
      - stats/**
      - validate_version_added_fields_in_config.py
      - verify_release_calendar.py
---

# airflow_breeze External Client Boundary Integration: Consumer Inspect Project Lock Artifact Resolve

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `airflow_perf/dags/**`
- `backport/**`
- `breeze/src/airflow_breeze/**`
- `ide_setup/**`
- `registry/**`
- `send_email.py`
- `stats/**`
- `validate_version_added_fields_in_config.py`
- `verify_release_calendar.py`

## Context

- Internal API modules frequently interface with heterogeneous external boundary sources including cloud provider clients, remote HTTP endpoints, operating system process environments, and serialized document payloads.
- External client payloads and process environments exhibit variable availability, optional fields, and schema drift across execution contexts.
- Unchecked subscription access on unvalidated boundary structures introduces unhandled key exceptions, leading to brittle integration boundaries and premature execution termination.
- A uniform defensive boundary consumption convention ensures reliable execution across varied local and automated execution environments.

## Problem Statement

Internal integration APIs that interact with external services, remote client endpoints, and process environment variables risk runtime failure when external payloads drift, omit optional fields, or encounter unconfigured environment settings. Direct key subscription on raw external payloads causes uncaught runtime errors that disrupt orchestration workflows without clear boundary failure containment.

## Decision

- **R-IA-001** MUST: The consumer MUST inspect the project lock artifact to resolve and verify the exact locked version of all external client and integration libraries before implementing boundary interaction contracts.

## Policy Block

In scope:

- All internal API modules, parameter classes, and orchestration commands that interface with external clients, cloud services, HTTP responses, or process environment variables.
- Boundary parsing operations handling serialized data structures from external systems.

Out of scope:

- Internal strongly-typed domain model interactions where fields are validated and guaranteed by internal type contracts.
- Internal database model querying governed by schema-validated object-relational mapping definitions.

Exceptions:

- **EXC-001** An external client response has already been validated against a strict runtime schema validator prior to internal access.
  Approval: Architecture Review Team
  Documentation: Record the schema validation step and guaranteed invariant in the module integration documentation.

## Rationale

- Evidence across sixty-two codebase integration files demonstrates uniform adoption of fallback-capable accessors when extracting values from external clients, third-party responses, and process environments.
- Using defensive retrieval decouples internal API consumers from transient schema variability and missing optional attributes in external integrations.
- Standardizing on explicit fallback values prevents unhandled missing-key exceptions during boundary ingestion without requiring heavy boilerplate exception-handling blocks.

## Consequences

Positive:

- Prevents unhandled runtime key exceptions when external services or execution environments omit optional fields.
- Ensures resilient boundary mediation across diverse local and automated runtime environments.
- Reduces boilerplate exception handling logic across internal integration command handlers.

Negative:

- Defensive fallbacks may quietly mask upstream service contract changes or attribute renaming.
- Default fallback selections can propagate unintended state downstream if default values are not rigorously aligned with domain expectations.

## Alternatives

- Direct subscription indexing with granular try-except exception blocks around every boundary property lookup. (rejected)
  Rejected because: Creates verbose repetitive exception handling across integration modules and obscures internal API contract logic.
  When valid: When missing attributes represent catastrophic failure modes that require immediate specialized abort sequences.
- Mandatory schema model validation layers for all external client responses and environment mappings prior to internal API ingestion. (deferred)
  Rejected because: Introduces substantial runtime serialization overhead and maintenance complexity for lightweight internal commands and developer utilities.
  When valid: When interfacing with untrusted third-party public API endpoints that require strict input sanitization contracts.

## Risks

- Silent failure or unexpected downstream behavior when external services change response field names.
  Mitigation: Pair defensive retrieval with boundary logging or integration smoke tests that verify critical payload paths.
  Owner: Engineering Team
- Inconsistent fallback default types causing downstream type errors during value transformation.
  Mitigation: Enforce strict fallback type alignment matching the expected contract type through integration tests and static typing.
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
- Encapsulate external environment lookups and third-party client payload extraction in dedicated parameter builders or utility modules rather than scattering raw accessors across business commands.
- Provide explicit fallback defaults that strictly match the expected downstream consumer type to avoid mixing null values with collection or scalar operations.

## Continuation Context

Verify commands:

- Discover and run the project test execution script targeting internal API integration suites to verify boundary handling routines.
- Discover and run the repository static analysis linter to detect unhandled key subscripting on external boundary objects.

Accept when:

- All internal API integration tests pass without raising unhandled missing-key exceptions when optional boundary fields are absent.
- Static analysis checks confirm boundary attribute accessors provide explicit fallback defaults across integration interfaces.

## Enforcement

- Verified by: Automated continuous integration test suites executing integration and unit test scenarios.
- Verified by: Peer code review verifying that external client data extraction adheres to defensive accessor standards.
- Violation handling: Pull requests containing direct dictionary indexing on external payloads or unvalidated environment lookups MUST be blocked until defensive accessors or validation boundaries are introduced.
- Exception process: Teams requesting exemptions for strict fail-fast validation boundaries MUST submit an architectural exemption detailing upstream schema guarantees and error-handling strategies.
