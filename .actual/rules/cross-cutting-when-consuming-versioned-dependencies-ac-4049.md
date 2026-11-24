---
version: 2
name: cross-cutting-when-consuming-versioned-dependencies-ac-4049
description: "Standard Library Core Modules: When Consuming Versioned Dependencies Across Runtime"
metadata:
  actual:
    adr_id: 4049fe8f-e578-4809-ae21-565a4c2c9090
    status: accepted
    candidate_group_id: e3e3a60d-aa58-47d8-82e5-7865894075f6
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# Standard Library Core Modules: When Consuming Versioned Dependencies Across Runtime

Scope: applies to `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- Command-line execution workflows, authentication handlers, and client boundary modules requiring operating system access and data serialization.

### Rules

- **R-LM-001** MUST: When consuming versioned dependencies across runtime boundaries, the consumer MUST inspect the project lock file to verify resolved dependency versions prior to integration.

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
