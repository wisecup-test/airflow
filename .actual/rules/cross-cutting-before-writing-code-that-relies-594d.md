---
version: 2
name: cross-cutting-before-writing-code-that-relies-594d
description: "airflowctl Runtime Environment Variable Configuration Management: Before Writing Code That Relies External"
metadata:
  actual:
    adr_id: 594d5095-93e0-4b57-9154-407b41248c4b
    status: accepted
    candidate_group_id: 4facb53a-ebe2-4197-bb25-e8230965bd29
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/**
---

# airflowctl Runtime Environment Variable Configuration Management: Before Writing Code That Relies External

Scope: applies to `airflow-ctl/docs/**`, `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- All runtime command handlers, API clients, and execution hooks within the package.
- All runtime settings governing debugging, authentication credentials, retry thresholds, and target environments.

### Rules

- **R-CEM-001** MUST: Before writing code that relies on external dependencies or environment resolution libraries, developers MUST inspect the repository lock artifact and verify the exact resolved version against public reference documentation.

### Verify

- Discover the project test runner and execute the automated test suite covering configuration and client runtime modules.
- Discover and run static analysis rules that check for standardized environment variable access patterns across the repository.

**Accept when:**

- Environment variable reads across client and command boundaries pass automated verification without direct undeclared access.
- Configuration precedence correctly prioritizes explicit command arguments over environment variables with fallback to internal defaults.
- Automated test suites pass with isolated environment variables verifying repeatable behavior.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Static analysis checks verifying environment access calls adhere to established namespace conventions.
- Verified by: Automated test suites running across isolated environment variable states.
- Verified by: Peer code reviews inspecting all runtime configuration resolution routines.
- Violation handling: Pull requests containing unnamespaced or undeclared environment variable access will fail automated quality gates.
- Violation handling: Direct disk persistence of tokens or secrets discovered in place of environment sourcing will be rejected immediately.
- Violation handling: Violations detected in mainline branches require immediate remediation patches or reversion.
- Exception process: Submit an architecture review proposal detailing the technical limitation preventing standard environment configuration sourcing.
- Exception process: Obtain explicit sign-off from the technical leads before implementing alternative configuration ingestion paths.
- Exception process: Document the approved deviation in module documentation with a scheduled deprecation date.
</enforcement>
