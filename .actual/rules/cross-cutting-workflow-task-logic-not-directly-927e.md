---
version: 2
name: cross-cutting-workflow-task-logic-not-directly-927e
description: "Adoption of airflow.sdk for Workflow and Task Definition: Workflow Task Logic Not Directly Access"
metadata:
  actual:
    adr_id: 927e1d7a-62ec-4237-82c6-8f0410c57b7c
    status: accepted
    candidate_group_id: 42f92a7f-c357-4f51-84d8-f32a901679a0
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of airflow.sdk for Workflow and Task Definition: Workflow Task Logic Not Directly Access

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Authoring workflow definitions, data pipelines, and scheduled DAGs across repository modules.
- Implementing task execution functions, data transformation steps, and asset-based triggers.

### Rules

- **R-LM-001** MUST_NOT: Workflow task logic MUST NOT directly access internal database session models or private orchestrator state tables, adhering strictly to the public interfaces exposed by airflow.sdk.

### Verify

- Discover the repository test runner from the root configuration and execute unit and integration test suites covering workflow definitions.
- Discover the static analysis and linting verification scripts from the repository build configuration and execute them to check module import boundaries.

**Accept when:**

- All discovered workflow and task test suites execute and pass without import or runtime errors.
- Static analysis checks confirm all task definitions and workflow declarations reference the approved SDK module without unauthorized internal model access.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test and lint pipelines executed against proposed changes.
- Verified by: Peer code review verifying compliance with SDK import standards and task decorator conventions.
- Violation handling: Automated build failure when unapproved internal model imports or deprecated operator patterns are detected.
- Violation handling: Rejection of pull requests introducing direct database queries or non-standard task execution wrappers.
- Exception process: Formal architectural review submission documenting specific technical limitations requiring direct internal model access.
- Exception process: Temporary time-bound waiver approved by platform engineering leadership with scheduled migration milestones.
</enforcement>
