---
version: 2
name: cross-cutting-external-client-instances-authentication-5cbc
description: "Akeyless SDK Integration and Connection Verification: External Client Instances Authentication Tokens Cached"
metadata:
  actual:
    adr_id: 5cbcc9d1-8f31-4525-a2e4-207f3096a6c5
    status: accepted
    candidate_group_id: 2515d51b-34a7-4d49-bef1-9db4b29a3cdb
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/providers/akeyless/**
---

# Akeyless SDK Integration and Connection Verification: External Client Instances Authentication Tokens Cached

Scope: applies to `providers/akeyless/src/airflow/providers/akeyless/**`.

These rules are ALWAYS ACTIVE for:
- Integration hooks and secret backend services interacting with the Akeyless Vault Platform.

### Rules

- **R-TS-001** SHOULD: External client instances and authentication tokens SHOULD be cached across invocations to avoid redundant network handshakes during repeated secret resolution requests.

### Verify

- Discover and execute the project integration test runner that validates external client connection testing routines.
- Discover and run the project static analysis and linting suite to confirm client instantiation compliance across integration modules.

**Accept when:**

- Integration test suites pass with successful connectivity verification via the test_connection interface.
- All secret retrieval and authentication paths instantiate the akeyless client through configured API client abstractions without unhandled exceptions.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis rules verifying that external vault calls route through approved SDK client abstractions.
- Verified by: Continuous integration pipeline executing test suites verifying test_connection implementations against mock or live vault endpoints.
- Verified by: Peer code review on all pull requests touching hook or secrets backend modules.
- Violation handling: Pull requests introducing unauthorized HTTP clients or bypassing client instantiation standards are blocked at code review.
- Violation handling: Static analysis failures block pull request merging until the implementation complies with client architecture rules.
- Exception process: Submit an architectural deviation request detailing why direct vault interaction or alternative client implementation is required.
- Exception process: Security and Platform leads review and approve temporary exemptions with an explicit remediation timeline.
</enforcement>
