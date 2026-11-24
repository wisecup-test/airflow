---
version: 2
name: cross-cutting-consumers-inspect-repository-dependency-0412
description: "Adoption of the logging Core Module for Diagnostic and Event Logging: Consumers Inspect Repository Dependency Manifest Lock"
metadata:
  actual:
    adr_id: 04123e4e-1b74-477f-9be9-4d171204bba6
    status: accepted
    candidate_group_id: 15b1d632-8fff-47d3-86dc-22c1a699cf3b
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/**
---

# Adoption of the logging Core Module for Diagnostic and Event Logging: Consumers Inspect Repository Dependency Manifest Lock

Scope: applies to `airflow-ctl/docs/**`, `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- Internal application modules requiring diagnostic event capture, operational logging, or warning emission.
- Subsystems interfacing with retry handlers, error handlers, or documentation processors that consume log levels.

### Rules

- **R-LM-001** MUST: Consumers MUST inspect the repository dependency manifest and lock artifacts to determine exact resolved versions before implementing or extending logging dependencies.

### Verify

- Discover and run the project static analysis and linting checks to verify module logger acquisition compliance.
- Discover and execute the repository test suite to ensure diagnostic logging and log-level assertions pass.

**Accept when:**

- All modules acquire loggers via the approved factory interface without direct instantiation.
- Automated test suites and static analysis checks pass with zero logging-related errors or warnings.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis and linting rules executed in continuous integration pipelines.
- Verified by: Peer code review verification of new modules and logging integration points.
- Violation handling: Automated continuous integration build failure on non-compliant logger acquisitions.
- Violation handling: Code review rejection of unapproved logging patterns or direct stream writing.
- Exception process: Formal architectural review request documenting technical necessity and alternative mitigation strategies.
</enforcement>
