---
version: 2
name: cross-cutting-before-implementing-modifying-code-that-f775
description: "Adoption of Akeyless SDK for Centralized Secrets and Credential Management: Before Implementing Modifying Code That Depends"
metadata:
  actual:
    adr_id: f775a61a-4290-4adb-9463-b68949d53e64
    status: accepted
    candidate_group_id: 29dd61c9-798c-4bde-980c-5a872c53a794
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/providers/akeyless/**
---

# Adoption of Akeyless SDK for Centralized Secrets and Credential Management: Before Implementing Modifying Code That Depends

Scope: applies to `providers/akeyless/src/airflow/providers/akeyless/**`.

These rules are ALWAYS ACTIVE for:
- All provider modules interacting with the external vault platform for credentials, configuration, and variables.
- Workflow execution components requiring dynamic, rotated, or static secret retrieval.

### Rules

- **R-LM-001** MUST: Before implementing or modifying code that depends on the client library, consumers MUST inspect the repository lock artifact to determine the exact resolved dependency version and verify API compatibility against official documentation.

### Verify

- Run the repository test runner targeting provider secret retrieval and hook integration test suites.
- Execute the repository static analysis and linting tools across provider packages to ensure compliance with client usage rules.

**Accept when:**

- All provider integration tests for secret retrieval and connection resolution pass against the mock or live vault endpoint.
- Client initialization and token management verify that API tokens are cached and refreshed before expiry without unhandled authentication exceptions.
- Lock-version verification confirms that the resolved client library matches the manifest constraints and exposes all required interfaces.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis verifying that direct HTTP calls are not substituted for SDK client interfaces within provider components.
- Verified by: Continuous integration test suites validating secret retrieval and backend resolution using mock API endpoints.
- Verified by: Peer code reviews ensuring adherence to token caching and error translation rules.
- Violation handling: Pull requests implementing bespoke API callers or bypassing the SDK client shall fail automated verification.
- Violation handling: Violations detected during code review must be refactored to use standard hook and backend structures before merging.
- Violation handling: Unapproved credential access patterns will trigger security review notifications.
- Exception process: Submit an architectural exception request detailing technical constraints preventing standard SDK client integration.
- Exception process: Obtain security architecture sign-off for alternative secret resolution mechanisms.
- Exception process: Document approved deviations with an explicit timeline for remediation.
</enforcement>
