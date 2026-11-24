---
version: 2
name: cross-cutting-modules-capturing-failure-diagnostics-ac-d9d7
description: "traceback Module Adoption for Stack Trace Formatting and Diagnostics Serialization: Modules Capturing Failure Diagnostics Across Boundaries"
metadata:
  actual:
    adr_id: d9d7ec40-9cdb-4cd3-b619-b67dcbfac775
    status: accepted
    candidate_group_id: b0a4104a-d002-456c-a245-2aedccc1b311
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# traceback Module Adoption for Stack Trace Formatting and Diagnostics Serialization: Modules Capturing Failure Diagnostics Across Boundaries

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Exception handling routines operating at API server boundaries.
- Asynchronous trigger workers and event generators yielding failure payloads.
- Background file processing and parsing modules recording diagnostic errors for persistence.

### Rules

- **R-LM-001** MUST: Modules capturing failure diagnostics across API boundaries, asynchronous trigger channels, and processor boundaries MUST adopt the traceback module to extract and format stack traces into structured text.

### Verify

- Discover the project static analysis and linting configuration from repository manifests and run the suite to verify compliance with core library import standards.
- Discover the automated test runner configured in the repository and execute test suites covering boundary exception handlers to verify traceback serialization behavior.

**Accept when:**

- Static analysis passes with no unauthorized error formatting routines or direct raw exception transports across boundary modules.
- Automated tests confirm that exception handlers produce correlated log entries and sanitized responses across boundary interfaces.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration static analysis checks verifying standardized error handling and core library imports.
- Verified by: Peer code reviews covering boundary interfaces, HTTP handlers, and asynchronous event triggers.
- Violation handling: Pull requests containing direct raw exception transmission or custom stack trace string parsing will be blocked in review.
- Violation handling: Automated linting failures will reject builds that bypass standardized boundary error handlers.
- Exception process: Submit an architectural review request outlining performance constraints or specialized third-party diagnostic integration requirements for formal review.
</enforcement>
