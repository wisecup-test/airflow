---
version: 2
name: cross-cutting-developers-not-introduce-third-party-c5f2
description: "datetime Standard Library Module: Developers Not Introduce Third Party Date"
metadata:
  actual:
    adr_id: c5f2f293-746c-46ba-b07d-7f610d804b04
    status: accepted
    candidate_group_id: eb1c26b1-99a8-4e1f-8035-ff8a14519a9b
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# datetime Standard Library Module: Developers Not Introduce Third Party Date

Scope: applies to `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- All command implementations, data transfer models, and API client operations handling date, time, or timestamp representations.

### Rules

- **R-LM-001** MUST_NOT: Developers MUST NOT introduce third-party date parsing or manipulation libraries unless an approved exception demonstrates standard library datetime inadequacy.

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
