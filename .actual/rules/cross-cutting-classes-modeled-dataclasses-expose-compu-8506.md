---
version: 2
name: cross-cutting-classes-modeled-dataclasses-expose-compu-8506
description: "Adoption of dataclasses Module for Structured Parameter and Model Definitions: Classes Modeled Dataclasses Expose Computed Properties"
metadata:
  actual:
    adr_id: 85066897-72bc-4a3c-bb28-aa4eae6c7493
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

# Adoption of dataclasses Module for Structured Parameter and Model Definitions: Classes Modeled Dataclasses Expose Computed Properties

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/registry/**`.

These rules are ALWAYS ACTIVE for:
- All internal parameter models, build configuration structures, and execution option containers.

### Rules

- **R-LM-001** MAY: Classes modeled with dataclasses MAY expose computed properties to derive formatted arguments and conditional values from declared field state.

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
