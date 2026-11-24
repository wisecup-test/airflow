---
version: 2
name: cross-cutting-derived-attributes-secondary-initializat-9f04
description: "dataclasses Domain and Parameter Model Architecture: Derived Attributes Secondary Initializations That Depend"
metadata:
  actual:
    adr_id: 9f0442aa-890d-42fc-96ff-2472a9b7c98f
    status: accepted
    candidate_group_id: 7d219bf7-1bb5-440b-9598-6eb6541f4c38
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - registry/**
      - stats/**
      - verify_release_calendar.py
---

# dataclasses Domain and Parameter Model Architecture: Derived Attributes Secondary Initializations That Depend

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/registry/**`, `dev/stats/**`, `dev/verify_release_calendar.py`.

These rules are ALWAYS ACTIVE for:
- Domain entities, parameter definitions, and operational configuration state across tooling and operational modules.
- Hierarchical parameter models that generate command arguments, execution contexts, and artifact metadata.

### Rules

- **R-DM-001** SHOULD: Derived attributes or secondary initializations that depend on primary fields SHOULD be executed within the __post_init__ lifecycle hook.

### Verify

- Discover the repository type verification runner from the project configuration and execute it across domain modeling modules.
- Discover and run the project test suite covering parameter models and domain entities to ensure attribute resolution passes.

**Accept when:**

- All domain and parameter dataclasses pass static type inspection with zero unresolved type or attribute errors.
- All test suites verifying parameter models and domain entity initialization execute successfully without regression.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration type-checking and automated test verification workflows.
- Verified by: Peer code review evaluating domain model structure and typing contracts.
- Violation handling: Pull requests introducing raw parameter dictionaries or untyped data classes will be blocked during review.
- Violation handling: Continuous integration failures on type verification must be resolved prior to merging.
- Exception process: Submit an architectural proposal detailing why dataclass modeling is unsuited for the specific component, subject to maintainer consensus.
</enforcement>
