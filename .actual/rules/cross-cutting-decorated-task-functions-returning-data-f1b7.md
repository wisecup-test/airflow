---
version: 2
name: cross-cutting-decorated-task-functions-returning-data-f1b7
description: "Adoption of airflow.sdk Task Decorators for Workflow Task Integration: Decorated Task Functions Returning Data Across"
metadata:
  actual:
    adr_id: f1b790a2-842d-4f97-8c9a-1feefd0f0de8
    status: accepted
    candidate_group_id: 3b07933a-4059-4000-8ad1-7153016dbd50
    evidence:
      source: deep
  scope:
    root: kubernetes-tests
    paths:
      - lang_sdk/dags/**
---

# Adoption of airflow.sdk Task Decorators for Workflow Task Integration: Decorated Task Functions Returning Data Across

Scope: applies to `kubernetes-tests/lang_sdk/dags/**`.

These rules are ALWAYS ACTIVE for:
- Workflow pipeline definition modules that declare task execution units and data passing boundaries.

### Rules

- **R-IP-001** SHOULD: Decorated task functions returning data across task boundaries SHOULD return serializable values to allow boundary message passing between downstream tasks.

### Verify

- Discover and execute the project verification script for workflow pipeline integrity and task definition syntax.
- Execute the repository test suite for pipeline integration contracts to confirm decorated tasks execute without serialization errors.

**Accept when:**

- All workflow tasks declared with the task decorator pass pipeline validation tests without syntax or dependency errors.
- Inter-task data exchanges across decorated task boundaries execute and serialize successfully in the pipeline test suite.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline testing and static analysis linters.
- Verified by: Peer code review for all modifications to workflow pipeline task definitions.
- Violation handling: Pull requests introducing undecorated task functions or invalid task boundary contracts are blocked until corrected.
- Exception process: Exceptions require documented technical justification and approval from the pipeline architecture team.
</enforcement>
