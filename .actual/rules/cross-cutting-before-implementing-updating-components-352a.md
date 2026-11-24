---
version: 2
name: cross-cutting-before-implementing-updating-components-352a
description: "Adoption of airflow.sdk for Workflow and Task Definition: Before Implementing Updating Components Depending Sdk"
metadata:
  actual:
    adr_id: 352a80b5-d36a-4e92-af62-4a13d1d570ab
    status: accepted
    candidate_group_id: 42f92a7f-c357-4f51-84d8-f32a901679a0
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of airflow.sdk for Workflow and Task Definition: Before Implementing Updating Components Depending Sdk

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Authoring workflow definitions, data pipelines, and scheduled DAGs across repository modules.
- Implementing task execution functions, data transformation steps, and asset-based triggers.

### Rules

- **R-LM-001** MUST: Before implementing or updating components depending on the SDK library, the consumer MUST inspect the repository dependency declaration and authoritative lock artifact to verify the exact resolved library version.

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
