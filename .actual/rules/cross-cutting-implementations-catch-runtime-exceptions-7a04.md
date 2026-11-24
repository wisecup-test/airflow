---
version: 2
name: cross-cutting-implementations-catch-runtime-exceptions-7a04
description: "Airflow BaseHook test_connection Contract Implementation: Implementations Catch Runtime Exceptions During Authentication"
metadata:
  actual:
    adr_id: 7a04f2a6-13b6-4fe8-969e-796f1d73c832
    status: accepted
    candidate_group_id: f8a7454a-ba33-4378-b37b-22d738843b53
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/providers/akeyless/hooks/**
---

# Airflow BaseHook test_connection Contract Implementation: Implementations Catch Runtime Exceptions During Authentication

Scope: applies to `providers/akeyless/src/airflow/providers/akeyless/hooks/**`.

These rules are ALWAYS ACTIVE for:
- Custom hook classes extending BaseHook interfacing with external authentication and credential services

### Rules

- **R-TS-001** SHOULD: Implementations SHOULD catch runtime exceptions during authentication attempts and return the caught error description rather than allowing unhandled exceptions to escape

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
