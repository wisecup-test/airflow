---
version: 2
name: cross-cutting-boundary-mapping-functions-define-type-db44
description: "airflow_breeze External Client Boundary Integration: Boundary Mapping Functions Define Type Appropriate"
metadata:
  actual:
    adr_id: db44ec32-bc12-48ba-a0db-a0a5ea286fb5
    status: accepted
    candidate_group_id: a27b0bc4-eeae-4ed7-841f-347fc5e9dfd7
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
      - backport/**
      - breeze/src/airflow_breeze/**
      - ide_setup/**
      - registry/**
      - send_email.py
      - stats/**
      - validate_version_added_fields_in_config.py
      - verify_release_calendar.py
---

# airflow_breeze External Client Boundary Integration: Boundary Mapping Functions Define Type Appropriate

Scope: applies to `dev/airflow_perf/dags/**`, `dev/backport/**`, `dev/breeze/src/airflow_breeze/**`, `dev/ide_setup/**`, `dev/registry/**`, `dev/send_email.py`, `dev/stats/**`, `dev/validate_version_added_fields_in_config.py`, `dev/verify_release_calendar.py`.

These rules are ALWAYS ACTIVE for:
- All internal API modules, parameter classes, and orchestration commands that interface with external clients, cloud services, HTTP responses, or process environment variables.
- Boundary parsing operations handling serialized data structures from external systems.

### Rules

- **R-IA-001** SHOULD: Boundary mapping functions SHOULD define type-appropriate fallback defaults reflecting empty collections, default booleans, or neutral string sentinels to preserve downstream contract invariant expectations.

### Verify

- Discover and run the project test execution script targeting internal API integration suites to verify boundary handling routines.
- Discover and run the repository static analysis linter to detect unhandled key subscripting on external boundary objects.

**Accept when:**

- All internal API integration tests pass without raising unhandled missing-key exceptions when optional boundary fields are absent.
- Static analysis checks confirm boundary attribute accessors provide explicit fallback defaults across integration interfaces.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites executing integration and unit test scenarios.
- Verified by: Peer code review verifying that external client data extraction adheres to defensive accessor standards.
- Violation handling: Pull requests containing direct dictionary indexing on external payloads or unvalidated environment lookups MUST be blocked until defensive accessors or validation boundaries are introduced.
- Exception process: Teams requesting exemptions for strict fail-fast validation boundaries MUST submit an architectural exemption detailing upstream schema guarantees and error-handling strategies.
</enforcement>
