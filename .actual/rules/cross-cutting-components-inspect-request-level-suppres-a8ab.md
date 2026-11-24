---
version: 2
name: cross-cutting-components-inspect-request-level-suppres-a8ab
description: "structlog Structured Logging Adoption: Components Inspect Request Level Suppression Flags"
metadata:
  actual:
    adr_id: a8ab44c0-ff2d-4662-bfe9-794dda60234c
    status: accepted
    candidate_group_id: d5d966c3-8e38-4435-a3dd-84786d044d45
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/**
---

# structlog Structured Logging Adoption: Components Inspect Request Level Suppression Flags

Scope: applies to `airflow-ctl/docs/**`, `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- Implementation of API client services, HTTP operations, network request retry layers, and remote service telemetry.

### Rules

- **R-L-001** MAY: Components MAY inspect request-level suppression flags to selectively silence non-actionable error logging during anticipated failure scenarios.

### Verify

- Discover the project test runner and execute the test suite covering client telemetry and logging configurations.
- Discover the static analysis tool configured in the repository and verify logger import consistency across API client modules.

**Accept when:**

- The test suite completes with all structured logging assertions passing without errors.
- Static analysis validates that API client and operational modules instantiate loggers using the structured logging factory.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks executing the project test suite and static analysis rules.
- Verified by: Peer code review verifying logger factory imports and contextual event binding on new API endpoints.
- Violation handling: Pull requests introducing unstructured logging or direct standard library loggers within client operational boundaries will be blocked.
- Violation handling: Violations identified during code review must be refactored to use the structured logging factory before merging.
- Exception process: Exceptions for non-operational utility scripts or standalone tooling must be documented and approved by the engineering leads.
</enforcement>
