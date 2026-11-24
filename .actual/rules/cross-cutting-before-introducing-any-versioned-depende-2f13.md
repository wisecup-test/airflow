---
version: 2
name: cross-cutting-before-introducing-any-versioned-depende-2f13
description: "datetime Standard Library Module: Before Introducing Any Versioned Dependency Complementing"
metadata:
  actual:
    adr_id: 2f1317a2-10fd-4aab-8ae5-c0c318fe5345
    status: accepted
    candidate_group_id: eb1c26b1-99a8-4e1f-8035-ff8a14519a9b
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# datetime Standard Library Module: Before Introducing Any Versioned Dependency Complementing

Scope: applies to `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- All command implementations, data transfer models, and API client operations handling date, time, or timestamp representations.

### Rules

- **R-LM-001** MUST: Before introducing any versioned dependency complementing standard library temporal modules, the consumer MUST inspect the repository lock or resolution artifact to determine the exact resolved dependency version.

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
