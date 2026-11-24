---
version: 2
name: cross-cutting-internal-parameter-schemas-configuration-f4b2
description: "Adoption of dataclasses Module for Structured Parameter and Model Definitions: Internal Parameter Schemas Configuration Data Containers"
metadata:
  actual:
    adr_id: f4b2ac60-13a3-49da-b110-39357dc25dc6
    status: accepted
    candidate_group_id: 1bf56d00-aa7e-4970-863a-6e9e23fcb661
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - registry/**
---

# Adoption of dataclasses Module for Structured Parameter and Model Definitions: Internal Parameter Schemas Configuration Data Containers

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/registry/**`.

These rules are ALWAYS ACTIVE for:
- All internal parameter models, build configuration structures, and execution option containers.

### Rules

- **R-LM-001** MUST: Internal parameter schemas and configuration data containers MUST be declared using the dataclasses module with explicit type annotations on every field.

### Verify

- Discover the repository static type analysis script from the project configuration and execute it across all parameter modules to verify field type compliance.
- Discover the test execution entrypoint defined in the project configuration and run the unit test suite covering parameter instantiation and inheritance.

**Accept when:**

- Static type checking passes across all parameter class declarations without typing errors.
- All unit tests validating parameter instantiation, default values, and computed properties execute successfully.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static type checking integrated into continuous integration pipelines.
- Verified by: Peer code review verifying adherence to dataclass conventions and absence of manual initializer boilerplate.
- Violation handling: Pull requests introducing manual initializer boilerplate or untyped parameter dictionaries will be blocked by static checks or review feedback.
- Violation handling: Non-compliant classes must be refactored to use standard dataclass definitions before merging.
- Exception process: Submit an exception request detailing why standard dataclasses cannot meet the structural or performance requirements.
- Exception process: Obtain written approval from the architecture review group and document the exception in the module header.
</enforcement>
