---
version: 2
name: cross-cutting-core-modules-service-boundaries-backgrou-1714
description: "Structured Logging Adoption via structlog: Core Modules Service Boundaries Background Workers"
metadata:
  actual:
    adr_id: 17149e46-997c-4f21-bde3-d2cb91e6fb12
    status: accepted
    candidate_group_id: d1769a16-5948-4d7e-a203-b592df67cd96
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Structured Logging Adoption via structlog: Core Modules Service Boundaries Background Workers

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All backend application services, HTTP API route controllers, execution workers, schedulers, and asynchronous triggers.
- All new modules introducing diagnostic telemetry or event logging.

### Rules

- **R-LM-001** MUST: All core modules, service boundaries, background workers, and API route handlers MUST use structlog as the standard structured logging library for emitting diagnostics, state changes, and runtime events.

### Verify

- Discover the repository static analysis and linting execution script from the workspace build configuration and run it to verify logger import conformity.
- Discover the repository test runner script and execute the test suite across affected modules to validate structured telemetry behavior.

**Accept when:**

- All module-level logging instances in backend, runtime, and API components initialize exclusively via structlog.get_logger without standard library logger bypasses.
- Automated repository test and static inspection checks pass with zero unstructured string formatting violations in log emission calls.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks enforcing logger import and instantiation rules in continuous integration.
- Verified by: Peer code reviews verifying structured key-value keyword usage instead of formatted string templates.
- Violation handling: Pull requests with unauthorized logger imports or unformatted logging invocations fail continuous integration gates.
- Violation handling: Violating code must be refactored to use structlog logger instances with structured attributes before approval.
- Exception process: Submit an architecture review request detailing why structured logging cannot meet component performance or dependency constraints.
- Exception process: Obtain formal approval from the platform engineering maintainers before merging non-conforming telemetry implementations.
- Exception process: Document any approved exception with an architectural justification in the repository architecture register.
</enforcement>
