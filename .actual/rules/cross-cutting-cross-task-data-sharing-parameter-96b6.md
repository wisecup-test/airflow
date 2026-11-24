---
version: 2
name: cross-cutting-cross-task-data-sharing-parameter-96b6
description: "Adoption of airflow.sdk for Workflow and Task Definition: Cross Task Data Sharing Parameter Passing"
metadata:
  actual:
    adr_id: 96b6cb6c-4ddc-4804-a7b6-f1447976275e
    status: accepted
    candidate_group_id: 42f92a7f-c357-4f51-84d8-f32a901679a0
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of airflow.sdk for Workflow and Task Definition: Cross Task Data Sharing Parameter Passing

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Authoring workflow definitions, data pipelines, and scheduled DAGs across repository modules.
- Implementing task execution functions, data transformation steps, and asset-based triggers.

### Rules

- **R-LM-001** SHOULD: Cross-task data sharing and parameter passing SHOULD use native function returns, dynamic parameter bindings, and context accessors defined within airflow.sdk.

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
