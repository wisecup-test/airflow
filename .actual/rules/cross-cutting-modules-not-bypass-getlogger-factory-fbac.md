---
version: 2
name: cross-cutting-modules-not-bypass-getlogger-factory-fbac
description: "Adoption of the logging Core Module for Diagnostic and Event Logging: Modules Not Bypass Getlogger Factory Interface"
metadata:
  actual:
    adr_id: fbaccbd8-df5a-4f34-9782-b508ecb62a00
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

# Adoption of the logging Core Module for Diagnostic and Event Logging: Modules Not Bypass Getlogger Factory Interface

Scope: applies to `airflow-ctl/docs/**`, `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- Internal application modules requiring diagnostic event capture, operational logging, or warning emission.
- Subsystems interfacing with retry handlers, error handlers, or documentation processors that consume log levels.

### Rules

- **R-LM-001** MUST_NOT: Modules MUST NOT bypass the getLogger factory interface through direct logger class instantiation.

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
