---
version: 2
name: cross-cutting-components-not-introduce-anonymous-lambd-6c81
description: "Adoption of Standard Operator Module for Functional Accessors and Entity Traversal: Components Not Introduce Anonymous Lambda Functions"
metadata:
  actual:
    adr_id: 6c81aa32-0d39-420e-a091-9a9739b9df0d
    status: accepted
    candidate_group_id: 9e6eb550-6726-4dd8-b1f3-be5503cf6c4a
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of Standard Operator Module for Functional Accessors and Entity Traversal: Components Not Introduce Anonymous Lambda Functions

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Core domain data models, workflow serialization definitions, API sorting services, and job scheduling components requiring attribute or method accessors.

### Rules

- **R-LM-001** MUST_NOT: Components MUST NOT introduce anonymous lambda functions for simple attribute retrieval or method invocation where corresponding operator module callables are available.

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
