---
version: 2
name: cross-cutting-parameter-specialization-hierarchies-inh-6123
description: "Adoption of dataclasses Module for Structured Parameter and Model Definitions: Parameter Specialization Hierarchies Inherit Common Base"
metadata:
  actual:
    adr_id: 61233ef8-07a5-4bf8-8a3a-0032604c52d5
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

# Adoption of dataclasses Module for Structured Parameter and Model Definitions: Parameter Specialization Hierarchies Inherit Common Base

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/registry/**`.

These rules are ALWAYS ACTIVE for:
- All internal parameter models, build configuration structures, and execution option containers.

### Rules

- **R-LM-001** SHOULD: Parameter specialization hierarchies SHOULD inherit from a common base dataclass to share common configuration attributes and normalization methods.

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
