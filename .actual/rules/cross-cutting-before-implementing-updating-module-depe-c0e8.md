---
version: 2
name: cross-cutting-before-implementing-updating-module-depe-c0e8
description: "Adoption of Standard Operator Module for Functional Accessors and Entity Traversal: Before Implementing Updating Module Dependencies That"
metadata:
  actual:
    adr_id: c0e8980b-90e3-4294-b777-b3f30704cae3
    status: accepted
    candidate_group_id: 9e6eb550-6726-4dd8-b1f3-be5503cf6c4a
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of Standard Operator Module for Functional Accessors and Entity Traversal: Before Implementing Updating Module Dependencies That

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Core domain data models, workflow serialization definitions, API sorting services, and job scheduling components requiring attribute or method accessors.

### Rules

- **R-LM-001** MUST: Before implementing or updating module dependencies that interface with core functional libraries, engineers MUST verify the exact locked runtime environment and language specifications from the repository resolution artifacts.

### Verify

- Discover the repository static analysis and linting configuration to execute automated checks enforcing functional accessor conventions.
- Discover the repository test execution runner and execute unit test suites covering serialized definitions and API service sorting mechanisms.

**Accept when:**

- Static analysis verification completes without reporting forbidden anonymous closures in accessor pipelines.
- All unit and integration test suites validating graph serialization and entity sorting pass with zero failures.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis and code quality inspections configured in continuous integration pipelines.
- Verified by: Mandatory peer code review verifying functional accessor conventions prior to branch merge.
- Violation handling: Pull requests containing unapproved anonymous lambda closures in place of standard operator callables will fail static analysis or be rejected during peer review.
- Exception process: Exceptions for multi-step closure logic must be formally documented with inline rationale and approved by a principal domain architect.
</enforcement>
