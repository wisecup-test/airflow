---
version: 2
name: cross-cutting-consumers-discover-project-dependency-ma-c914
description: "Internal API Client Architecture with airflowctl.api.client: Consumers Discover Project Dependency Manifest Resolve"
metadata:
  actual:
    adr_id: c914659f-a549-4cd6-a546-032f9216c16c
    status: accepted
    candidate_group_id: 2494e877-27cf-4197-bce4-f4df5012e098
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# Internal API Client Architecture with airflowctl.api.client: Consumers Discover Project Dependency Manifest Resolve

Scope: applies to `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- All command-line interface handlers interacting with internal REST services.
- All internal client operations and service adapter modules.

### Rules

- **R-IA-001** MUST: Consumers MUST discover the project dependency manifest and resolve the exact locked versions of the underlying HTTP client and serialization libraries from the authoritative repository resolution artifact before implementing or modifying client transport logic.

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
