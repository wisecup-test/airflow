---
version: 2
name: cross-cutting-before-implementing-modifying-schemas-ut-2699
description: "Pydantic Schema Validation for Domain Models and API Contracts: Before Implementing Modifying Schemas Utilizing Validation"
metadata:
  actual:
    adr_id: 269941d4-3842-4206-bd87-12de606a26c2
    status: accepted
    candidate_group_id: 49981b0c-6c01-4866-af66-e6deccdf65a3
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Pydantic Schema Validation for Domain Models and API Contracts: Before Implementing Modifying Schemas Utilizing Validation

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Definition of public and internal HTTP API request bodies, query parameters, and response structures
- Definition of inter-process serialization payloads and workload dispatch schemas across runtime execution boundaries
- Validation and transformation of untrusted inputs into structured domain transfer objects

### Rules

- **R-BRE-001** MUST: Before implementing or modifying schemas utilizing the validation library, developers MUST inspect the repository lock artifact to determine the exact resolved dependency version and verify API compatibility against documentation for that exact version.

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
