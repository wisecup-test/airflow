---
version: 2
name: cross-cutting-data-models-schemas-exposing-execution-8c5f
description: "Canonical Workflow Lifecycle State Management via airflow.utils.state: Data Models Schemas Exposing Execution Status"
metadata:
  actual:
    adr_id: 8c5fff74-3bde-4719-b6fd-f0673e751e0e
    status: accepted
    candidate_group_id: 5cf8ee29-1603-4e40-8d6d-4f9588a37604
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Canonical Workflow Lifecycle State Management via airflow.utils.state: Data Models Schemas Exposing Execution Status

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Task instance dependency evaluation and scheduling logic.
- API data models, serialization schemas, and status endpoints that report or ingest execution states.
- Internal state transition routines and task status management.

### Rules

- **R-LM-001** MUST: Data models and API schemas exposing execution status MUST type state fields with TaskInstanceState, DagRunState, or strictly controlled unions with literal sentinels.

### Verify

- Discover and run the project static analysis suite to verify that all task and DAG run state references import from the canonical state module.
- Discover and execute the project test suite covering task dependency evaluation and API schema validation to confirm state handling compliance.

**Accept when:**

- Static analysis validates zero occurrences of unmapped raw state strings in dependency checks and API models.
- All unit and integration tests for task dependency evaluation and state reporting pass successfully.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static typing checks during pull request evaluation.
- Verified by: Mandatory code reviews verifying that state handling imports exclusively from the canonical state module.
- Violation handling: Pull requests introducing raw string literals for execution states or bypassing canonical state models will be blocked.
- Exception process: Exceptions require formal architectural review and approval from the core framework maintainers.
</enforcement>
