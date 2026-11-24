---
version: 2
name: cross-cutting-test-connection-implementation-return-bo-afcc
description: "Airflow BaseHook test_connection Contract Implementation: Test Connection Implementation Return Boolean Success"
metadata:
  actual:
    adr_id: afcc3f32-e0f7-4877-82bd-f502ebb94462
    status: accepted
    candidate_group_id: f8a7454a-ba33-4378-b37b-22d738843b53
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/providers/akeyless/hooks/**
---

# Airflow BaseHook test_connection Contract Implementation: Test Connection Implementation Return Boolean Success

Scope: applies to `providers/akeyless/src/airflow/providers/akeyless/hooks/**`.

These rules are ALWAYS ACTIVE for:
- Custom hook classes extending BaseHook interfacing with external authentication and credential services

### Rules

- **R-TS-001** MUST: The test_connection implementation MUST return a boolean success indicator and a descriptive string message encapsulating status or failure details

### Verify

- Discover and run the project test suite targeting hook connection validation methods
- Discover and run static type checking across provider hook modules to validate method signatures

**Accept when:**

- Connection testing methods return a tuple containing a boolean flag and a descriptive string message
- Authentication exceptions are captured and formatted into error messages without raising unhandled exceptions

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated code review and continuous integration test suites
- Violation handling: Pull requests lacking required connectivity verification methods or exception handling must be revised prior to merge
- Exception process: Document justified exceptions in architectural review when integrating with third-party systems that do not expose interactive authentication
</enforcement>
