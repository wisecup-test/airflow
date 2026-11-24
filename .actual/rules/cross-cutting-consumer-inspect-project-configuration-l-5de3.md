---
version: 2
name: cross-cutting-consumer-inspect-project-configuration-l-5de3
description: "dataclasses Domain and Parameter Model Architecture: Consumer Inspect Project Configuration Lock Artifacts"
metadata:
  actual:
    adr_id: 5de3f8c3-e33b-4c38-9c5a-3185a53a2991
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

# dataclasses Domain and Parameter Model Architecture: Consumer Inspect Project Configuration Lock Artifacts

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/registry/**`, `dev/stats/**`, `dev/verify_release_calendar.py`.

These rules are ALWAYS ACTIVE for:
- Domain entities, parameter definitions, and operational configuration state across tooling and operational modules.
- Hierarchical parameter models that generate command arguments, execution contexts, and artifact metadata.

### Rules

- **R-DM-001** MUST: The consumer MUST inspect the project configuration and lock artifacts to verify runtime version compatibility before introducing or modifying dataclass domain models.

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
