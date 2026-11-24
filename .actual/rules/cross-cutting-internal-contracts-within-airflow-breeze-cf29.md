---
version: 2
name: cross-cutting-internal-contracts-within-airflow-breeze-cf29
description: "airflow_breeze External Client Boundary Integration: Internal Contracts Within Airflow Breeze That"
metadata:
  actual:
    adr_id: cf297811-1cb1-46fd-8acd-04e4223d0de7
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

# airflow_breeze External Client Boundary Integration: Internal Contracts Within Airflow Breeze That

Scope: applies to `dev/airflow_perf/dags/**`, `dev/backport/**`, `dev/breeze/src/airflow_breeze/**`, `dev/ide_setup/**`, `dev/registry/**`, `dev/send_email.py`, `dev/stats/**`, `dev/validate_version_added_fields_in_config.py`, `dev/verify_release_calendar.py`.

These rules are ALWAYS ACTIVE for:
- All internal API modules, parameter classes, and orchestration commands that interface with external clients, cloud services, HTTP responses, or process environment variables.
- Boundary parsing operations handling serialized data structures from external systems.

### Rules

- **R-IA-001** MUST: Internal API contracts within airflow_breeze that receive external client payloads, remote service responses, or process environment mappings MUST consume boundary attributes through defensive accessor functions with explicit fallback default values rather than direct key indexing.

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
