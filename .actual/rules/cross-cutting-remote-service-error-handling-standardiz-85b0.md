---
version: 2
name: cross-cutting-remote-service-error-handling-standardiz-85b0
description: "Internal API Client Architecture with airflowctl.api.client: Remote Service Error Handling Standardized Through"
metadata:
  actual:
    adr_id: 85b0edfc-af97-4d8e-a186-fb2caf2bea0d
    status: accepted
    candidate_group_id: 2494e877-27cf-4197-bce4-f4df5012e098
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# Internal API Client Architecture with airflowctl.api.client: Remote Service Error Handling Standardized Through

Scope: applies to `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- All command-line interface handlers interacting with internal REST services.
- All internal client operations and service adapter modules.

### Rules

- **R-IA-001** MUST: Remote service error handling MUST be standardized through ServerResponseError parsing and typed connection exceptions rather than raw HTTP transport status inspection in individual command modules.

### Verify

- discover_and_run_lint_checks
- discover_and_run_type_checks
- discover_and_run_unit_tests

**Accept when:**

- All command modules import internal service functionality exclusively from the designated client module without unmediated network calls.
- Static type analysis and linters confirm zero direct network library instantiations outside the client boundary.
- Test suite passes with all client operations asserting proper retry configuration and error translation.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline running static analysis and import boundary checks.
- Verified by: Peer code review verifying that new commands consume backend services solely through the client operations module.
- Violation handling: Pull requests introducing direct HTTP requests outside the designated client module fail automated linting and boundary validation checks.
- Violation handling: Violating implementations must be refactored to register operations within the client boundary before merge approval.
- Exception process: Architectural review submission justifying why the existing client boundary cannot accommodate the requirement, accompanied by an approved transition plan.
</enforcement>
