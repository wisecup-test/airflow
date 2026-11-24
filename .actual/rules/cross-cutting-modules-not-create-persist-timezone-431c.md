---
version: 2
name: cross-cutting-modules-not-create-persist-timezone-431c
description: "Pendulum Library Adoption for Timezone-Aware Datetime and Schedule Management: Modules Not Create Persist Timezone Naive"
metadata:
  actual:
    adr_id: 431c3ca5-1c90-4373-921b-c52b6bde438d
    status: accepted
    candidate_group_id: f7deb672-8439-4e12-a79b-6a45f983672e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Pendulum Library Adoption for Timezone-Aware Datetime and Schedule Management: Modules Not Create Persist Timezone Naive

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Core scheduling abstractions, data interval calculations, and timetable plugins
- Workflow definitions and task decorators performing date or time manipulation
- API decorators and logging layers handling request or execution timestamps

### Rules

- **R-LM-001** MUST_NOT: Modules MUST NOT create or persist timezone-naive datetime objects for schedule intervals, run markers, or timetable calculations.

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
