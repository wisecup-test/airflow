---
version: 2
name: cross-cutting-modules-responsible-workflow-scheduling-9a9c
description: "Pendulum Library Adoption for Timezone-Aware Datetime and Schedule Management: Modules Responsible Workflow Scheduling Timetable Evaluation"
metadata:
  actual:
    adr_id: 9a9cfd10-bc3b-4723-971c-d7cac5b51ca2
    status: accepted
    candidate_group_id: f7deb672-8439-4e12-a79b-6a45f983672e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Pendulum Library Adoption for Timezone-Aware Datetime and Schedule Management: Modules Responsible Workflow Scheduling Timetable Evaluation

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Core scheduling abstractions, data interval calculations, and timetable plugins
- Workflow definitions and task decorators performing date or time manipulation
- API decorators and logging layers handling request or execution timestamps

### Rules

- **R-LM-001** MUST: All modules responsible for workflow scheduling, timetable evaluation, data interval computation, and execution timestamp tracking MUST use the Pendulum library for temporal arithmetic and timezone resolution.

### Verify

- # Discover repository test runner and execute timetable and temporal calculation test suites
- # Discover static analysis and linting configuration to verify timezone-aware import adherence

**Accept when:**

- All automated test suites covering scheduling, timetables, and interval calculations pass without errors
- Static analysis confirms no naive datetime instantiations exist in scheduling and timetable paths
- Code review confirms all temporal arithmetic relies on the adopted library

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites
- Verified by: Static analysis and linter rules checking temporal imports
- Verified by: Mandatory peer code review for scheduling and timetable modules
- Violation handling: Pull requests introducing naive datetime arithmetic or unapproved datetime libraries are blocked from merging
- Violation handling: Violating code must be refactored to use the standardized Pendulum patterns before approval
- Exception process: Submit an architectural exception request detailing interoperability constraints with external systems
- Exception process: Approval required from core maintainers accompanied by explicit conversion boundary documentation
</enforcement>
