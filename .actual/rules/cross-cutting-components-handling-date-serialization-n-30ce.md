---
version: 2
name: cross-cutting-components-handling-date-serialization-n-30ce
description: "datetime Standard Library Module: Components Handling Date Serialization Normalize Timestamp"
metadata:
  actual:
    adr_id: 30ce9647-ab16-4701-9451-3f3a96278e1c
    status: accepted
    candidate_group_id: eb1c26b1-99a8-4e1f-8035-ff8a14519a9b
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# datetime Standard Library Module: Components Handling Date Serialization Normalize Timestamp

Scope: applies to `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- All command implementations, data transfer models, and API client operations handling date, time, or timestamp representations.

### Rules

- **R-LM-001** SHOULD: Components handling date serialization SHOULD normalize all timestamp objects to coordinated universal representations prior to external transmission.

### Verify

- Discover and run the project static analysis and linting scripts to verify compliance with standard library import rules.
- Discover and execute the automated test suite to ensure all date and timestamp serialization contracts pass without runtime errors.

**Accept when:**

- All modules pass static analysis verification with zero disallowed external date and time library imports.
- All automated test suites pass with temporal serialization validated against expected schema contracts.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis import checks during continuous integration.
- Verified by: Peer code review of data models and command implementations.
- Violation handling: Pull requests introducing unauthorized date or time libraries are blocked at continuous integration.
- Violation handling: Flagged violations require replacement with standard library datetime interfaces prior to merge.
- Exception process: Submit an architectural exception request detailing why standard library temporal capabilities cannot meet functional requirements.
- Exception process: Obtain formal review and approval from the architecture gatekeepers prior to adding external temporal dependencies.
</enforcement>
