---
version: 2
name: cross-cutting-workflow-pipeline-tasks-not-bypass-944d
description: "Adoption of airflow.sdk Task Decorators for Workflow Task Integration: Workflow Pipeline Tasks Not Bypass Declared"
metadata:
  actual:
    adr_id: 944d8768-a655-4031-907c-e73cb86da8c2
    status: accepted
    candidate_group_id: 3b07933a-4059-4000-8ad1-7153016dbd50
    evidence:
      source: deep
  scope:
    root: kubernetes-tests
    paths:
      - lang_sdk/dags/**
---

# Adoption of airflow.sdk Task Decorators for Workflow Task Integration: Workflow Pipeline Tasks Not Bypass Declared

Scope: applies to `kubernetes-tests/lang_sdk/dags/**`.

These rules are ALWAYS ACTIVE for:
- Workflow pipeline definition modules that declare task execution units and data passing boundaries.

### Rules

- **R-IP-001** MUST_NOT: Workflow pipeline tasks MUST NOT bypass the declared task decorator boundaries when coordinating data dependencies between dependent workflow stages.

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
