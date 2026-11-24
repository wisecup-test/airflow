---
version: 2
name: cross-cutting-external-request-response-data-models-4e92
description: "Pydantic Schema Validation for Domain Models and API Contracts: External Request Response Data Models Inter"
metadata:
  actual:
    adr_id: 4e92f962-4c54-4ce5-86d7-8708638be501
    status: accepted
    candidate_group_id: 49981b0c-6c01-4866-af66-e6deccdf65a3
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Pydantic Schema Validation for Domain Models and API Contracts: External Request Response Data Models Inter

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Definition of public and internal HTTP API request bodies, query parameters, and response structures
- Definition of inter-process serialization payloads and workload dispatch schemas across runtime execution boundaries
- Validation and transformation of untrusted inputs into structured domain transfer objects

### Rules

- **R-BRE-001** MUST: All external API request and response data models, inter-process communication payloads, and domain boundary transfer objects MUST be defined using Pydantic BaseModel or StrictBaseModel schemas to enforce explicit input validation and serialization contracts.

### Verify

- Discover and execute the repository test runner targeting schema validation and serialization suites.
- Discover and execute the repository static analysis tools to verify model type annotations and validator decorator signatures.

**Accept when:**

- All automated schema validation tests and boundary contract test suites execute successfully.
- Static type verification and lint analysis report zero errors across all model definitions and validator implementations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration pipelines verifying serialization and deserialization test suites
- Verified by: Static analysis linters enforcing model typing and validator method signatures
- Verified by: Peer code reviews validating that persistence entities are decoupled from boundary schemas
- Violation handling: Pull requests containing unvalidated boundary dictionaries or directly exposed persistence models are blocked by review gates
- Violation handling: Validation failures detected during automated testing halt build promotion until addressed
- Exception process: Architectural exceptions permitting unvalidated payloads or raw dictionary propagation must be submitted via formal architectural review with explicit rationale.
</enforcement>
