---
version: 2
name: cross-cutting-decorated-tasks-requiring-specialized-ru-476d
description: "Airflow SDK @task Decorator Integration Pattern: Decorated Tasks Requiring Specialized Runtime Routing"
metadata:
  actual:
    adr_id: 476d0ada-e5e1-4922-9de8-d594bf8e2c01
    status: accepted
    candidate_group_id: 92efd180-6006-4339-b017-3a6470acbc6d
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Airflow SDK @task Decorator Integration Pattern: Decorated Tasks Requiring Specialized Runtime Routing

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Workflow definitions and data integration pipelines authoring discrete execution tasks.
- Components integrating data transformation steps dispatched across queue and executor boundaries.

### Rules

- **R-IP-001** SHOULD: Decorated tasks requiring specialized runtime routing SHOULD explicitly declare runtime queue and executor configurations within decorator keyword arguments.

### Verify

- Execute the repository test runner discovered from the dependency manifest to run integration and unit test suites verifying task decorator execution.
- Run the repository static analysis and linting tools discovered from configuration manifests to validate decorator signatures and type annotations.

**Accept when:**

- Task decorator contracts and functional tasks execute without runtime parameter resolution failures during automated test runs.
- Static analysis validates that all task decorator configurations and return type annotations conform to project rules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration pipelines running automated unit and integration suites on workflow definitions.
- Verified by: Static analysis and linters validating task decorator signatures and parameter types.
- Verified by: Architecture-focused peer code review for all workflow and integration pipeline changes.
- Violation handling: Automated builds fail when task definitions produce non-serializable return objects across boundaries.
- Violation handling: Code reviews block pull requests introducing unnecessary operator subclassing where decorated functions suffice.
- Exception process: Submit an exception request detailing why procedural operator subclassing or bespoke execution wrappers are required over functional decorators.
- Exception process: Obtain sign-off from integration architecture maintainers before merging non-standard task implementations.
</enforcement>
