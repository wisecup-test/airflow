---
version: 2
name: cross-cutting-engineers-not-construct-custom-string-95e4
description: "traceback Module Adoption for Stack Trace Formatting and Diagnostics Serialization: Engineers Not Construct Custom String Formatting"
metadata:
  actual:
    adr_id: 95e458cd-882d-49ed-b594-16d2b649e46a
    status: accepted
    candidate_group_id: b0a4104a-d002-456c-a245-2aedccc1b311
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# traceback Module Adoption for Stack Trace Formatting and Diagnostics Serialization: Engineers Not Construct Custom String Formatting

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Exception handling routines operating at API server boundaries.
- Asynchronous trigger workers and event generators yielding failure payloads.
- Background file processing and parsing modules recording diagnostic errors for persistence.

### Rules

- **R-LM-001** SHOULD_NOT: Engineers SHOULD NOT construct custom string formatting routines for raw traceback objects or transmit unformatted exception instances across process communication boundaries.

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
