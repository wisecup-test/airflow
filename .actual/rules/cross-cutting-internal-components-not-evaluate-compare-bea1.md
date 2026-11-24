---
version: 2
name: cross-cutting-internal-components-not-evaluate-compare-bea1
description: "Enum Module String Enumeration Pattern for Domain State and API Contracts: Internal Components Not Evaluate Compare Domain"
metadata:
  actual:
    adr_id: bea17372-8596-487a-9bc4-cafbfbbfc05e
    status: accepted
    candidate_group_id: c0fd51b0-0b7d-4ecc-927f-6d720c54729b
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Enum Module String Enumeration Pattern for Domain State and API Contracts: Internal Components Not Evaluate Compare Domain

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Declaration of domain model states, execution lifecycle statuses, and task trigger rules.
- FastAPI request and response schemas modeling categorical data.
- Serialization definitions and protocol markers exchanged across process boundaries.
- Authorization resource actions and authentication permission categories.

### Rules

- **R-LM-001** MUST_NOT: Internal components MUST NOT evaluate or compare domain states using raw string literals where an enumeration class has been established.

### Verify

- Discover the project's static type verification tool from repository configuration and run it across all modules declaring enumeration classes.
- Discover the test runner from the project manifest and execute the test suite covering domain state transitions and API contract serialization.
- Discover the codebase linter and verify that no raw string literals are used in place of defined enumeration members.

**Accept when:**

- Static type analysis passes without errors across all enumeration usages and API contract models.
- All unit and integration tests verifying state transitions, serialization, and deserialization execute successfully.
- No raw string literals are detected where domain state enumerations are defined.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static type checking in CI pipelines verifying enumeration assignments.
- Verified by: Peer code review for pull requests modifying domain states or API schemas.
- Violation handling: Pull requests containing untyped string literals for domain state or unvalidated constants must be rejected.
- Violation handling: CI static analysis failures block merge until enumeration types are properly adopted.
- Exception process: Exceptions require documented architecture approval and an explicit migration plan to typed enumerations.
</enforcement>
