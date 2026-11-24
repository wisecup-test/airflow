---
version: 2
name: cross-cutting-dynamic-getter-functions-constructed-via-a78d
description: "Adoption of Standard Operator Module for Functional Accessors and Entity Traversal: Dynamic Getter Functions Constructed Via Operator"
metadata:
  actual:
    adr_id: a78dd2a1-d8d5-42a0-90f9-8f70fa1fede5
    status: accepted
    candidate_group_id: 9e6eb550-6726-4dd8-b1f3-be5503cf6c4a
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of Standard Operator Module for Functional Accessors and Entity Traversal: Dynamic Getter Functions Constructed Via Operator

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Core domain data models, workflow serialization definitions, API sorting services, and job scheduling components requiring attribute or method accessors.

### Rules

- **R-LM-001** SHOULD: Dynamic getter functions constructed via the operator module SHOULD be instantiated at module or class scope when reusable across invocations.

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
