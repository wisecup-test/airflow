---
version: 2
name: cross-cutting-test-connection-implementation-not-execu-0d2b
description: "Airflow BaseHook test_connection Contract Implementation: Test Connection Implementation Not Execute State"
metadata:
  actual:
    adr_id: 0d2bd22d-ade5-455b-a839-1e9c94051443
    status: accepted
    candidate_group_id: f8a7454a-ba33-4378-b37b-22d738843b53
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/providers/akeyless/hooks/**
---

# Airflow BaseHook test_connection Contract Implementation: Test Connection Implementation Not Execute State

Scope: applies to `providers/akeyless/src/airflow/providers/akeyless/hooks/**`.

These rules are ALWAYS ACTIVE for:
- Custom hook classes extending BaseHook interfacing with external authentication and credential services

### Rules

- **R-TS-001** MUST_NOT: The test_connection implementation MUST_NOT execute state-altering operations or fetch business secrets beyond what is strictly necessary to confirm authentication

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
