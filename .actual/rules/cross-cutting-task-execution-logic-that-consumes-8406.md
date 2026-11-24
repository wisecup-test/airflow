---
version: 2
name: cross-cutting-task-execution-logic-that-consumes-8406
description: "Adoption of airflow.sdk for Workflow Task Orchestration: Task Execution Logic That Consumes Runtime"
metadata:
  actual:
    adr_id: 8406aa6a-ad38-4928-8ab2-b30c1888e1c2
    status: accepted
    candidate_group_id: b96b8b42-63af-4816-aa21-0b9027376d89
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
---

# Adoption of airflow.sdk for Workflow Task Orchestration: Task Execution Logic That Consumes Runtime

Scope: applies to `dev/airflow_perf/dags/**`.

These rules are ALWAYS ACTIVE for:
- Workflow and directed acyclic graph definitions declared in the repository.
- Workload pipeline modules requiring task orchestration and dependency chaining.

### Rules

- **R-LM-001** MUST: Task execution logic that consumes runtime scheduling attributes MUST access task identifier and execution date properties through the task instance context rather than external state.

### Verify

- Discover and run the project test runner to execute workflow validation suites verifying DAG structure and task dependency graphs.
- Discover and run the repository static analysis linter to confirm adherence to approved module import paths and SDK conventions.

**Accept when:**

- All workflow definitions pass DAG structure checks and dependency resolution validation without errors.
- Task dependency graphs resolve deterministically with valid upstream relationships declared via the SDK interface.
- Static verification checks confirm that workflow modules do not reference unapproved internal engine runtime structures.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline executing static verification and workflow parsing checks.
- Verified by: Peer review on pull requests modifying workflow definition files.
- Violation handling: Pull requests introducing direct imports of deprecated or internal engine modules without SDK usage will be blocked.
- Violation handling: Workflow definitions failing DAG parsing or dependency resolution checks will fail the automated build.
- Exception process: Submit an architectural review request with justification when a legacy component cannot use the standard SDK module.
- Exception process: Document any approved temporary exemption in the central architectural registry.
</enforcement>
