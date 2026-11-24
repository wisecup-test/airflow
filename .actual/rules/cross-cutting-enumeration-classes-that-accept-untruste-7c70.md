---
version: 2
name: cross-cutting-enumeration-classes-that-accept-untruste-7c70
description: "Enum Module String Enumeration Pattern for Domain State and API Contracts: Enumeration Classes That Accept Untrusted External"
metadata:
  actual:
    adr_id: 7c70dbc8-44ed-40c9-889c-b31b49d1dcfb
    status: accepted
    candidate_group_id: c0fd51b0-0b7d-4ecc-927f-6d720c54729b
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Enum Module String Enumeration Pattern for Domain State and API Contracts: Enumeration Classes That Accept Untrusted External

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Declaration of domain model states, execution lifecycle statuses, and task trigger rules.
- FastAPI request and response schemas modeling categorical data.
- Serialization definitions and protocol markers exchanged across process boundaries.
- Authorization resource actions and authentication permission categories.

### Rules

- **R-LM-001** SHOULD: Enumeration classes that accept untrusted external input SHOULD implement validation classmethods that inspect member values via class members dictionaries.

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
