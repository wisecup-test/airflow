---
version: 2
name: cross-cutting-consumers-inspect-repository-dependency-a044
description: "Adoption of airflow.sdk Task Decorators for Workflow Task Integration: Consumers Inspect Repository Dependency Lock Artifact"
metadata:
  actual:
    adr_id: a0440495-49d7-4520-b52b-2325b3f0a20e
    status: accepted
    candidate_group_id: 3b07933a-4059-4000-8ad1-7153016dbd50
    evidence:
      source: deep
  scope:
    root: kubernetes-tests
    paths:
      - lang_sdk/dags/**
---

# Adoption of airflow.sdk Task Decorators for Workflow Task Integration: Consumers Inspect Repository Dependency Lock Artifact

Scope: applies to `kubernetes-tests/lang_sdk/dags/**`.

These rules are ALWAYS ACTIVE for:
- Workflow pipeline definition modules that declare task execution units and data passing boundaries.

### Rules

- **R-IP-001** MUST: Consumers MUST inspect the repository dependency lock artifact to determine the exact resolved version of airflow.sdk and verify that all invoked decorator interfaces exist within that version documentation.

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
