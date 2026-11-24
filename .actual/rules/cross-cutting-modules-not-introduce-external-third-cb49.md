---
version: 2
name: cross-cutting-modules-not-introduce-external-third-cb49
description: "Standard Library Core Modules: Modules Not Introduce External Third Party"
metadata:
  actual:
    adr_id: cb497a07-f718-4764-b672-224805a3eebc
    status: accepted
    candidate_group_id: e3e3a60d-aa58-47d8-82e5-7865894075f6
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# Standard Library Core Modules: Modules Not Introduce External Third Party

Scope: applies to `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- Command-line execution workflows, authentication handlers, and client boundary modules requiring operating system access and data serialization.

### Rules

- **R-LM-001** MUST_NOT: Modules MUST NOT introduce external third-party dependencies for trivial serialization or environment access tasks that standard library core modules already satisfy.

### Verify

- Discover and run the repository test suite to verify client authentication and configuration loading behavior.
- Discover and run the project static analysis and linting verification scripts to ensure dependency boundary adherence.

**Accept when:**

- All configuration parsing and system utility interactions pass automated test suites without unexpected runtime exceptions.
- Static analysis checks pass without flagging unvetted external dependencies for core system interactions.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suite execution.
- Verified by: Peer code review verification of new dependency introductions.
- Violation handling: Pull requests introducing redundant external libraries for standard library capabilities are blocked until remediated.
- Exception process: Architectural review and written justification submitted to the engineering team when standard library modules lack essential capabilities.
</enforcement>
