---
version: 2
name: cross-cutting-data-payloads-passed-airflowctl-client-44e3
description: "Adoption of airflowctl.api.client for CLI Command Remote Operations: Data Payloads Passed Airflowctl Client Structured"
metadata:
  actual:
    adr_id: 44e30276-b904-45a6-a68f-0ab415577672
    status: accepted
    candidate_group_id: 1a59d1a5-7c69-4cc2-8f66-a37d159163e9
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/ctl/commands/**
---

# Adoption of airflowctl.api.client for CLI Command Remote Operations: Data Payloads Passed Airflowctl Client Structured

Scope: applies to `airflow-ctl/src/airflowctl/ctl/commands/**`.

These rules are ALWAYS ACTIVE for:
- All CLI command handlers executing remote operations or managing configuration resources.
- Command handlers performing import, export, or synchronization workflows with backend services.

### Rules

- **R-LM-001** SHOULD: Data payloads passed to airflowctl.api.client SHOULD be structured using the generated datamodels contract definitions.

### Verify

- Discover and execute the repository static analysis and linting suite to verify compliance with boundary rules across command modules.
- Discover and run the project automated test suite to confirm command integration with client interfaces.

**Accept when:**

- Command implementations execute remote mutations exclusively through the designated API client interface.
- Local serialization and deserialization routines map directly to defined contract models without ad-hoc network requests.
- Project verification and static analysis suites report zero violations across command handlers.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks inspecting imports and call boundaries across command modules.
- Verified by: Peer code review verifying adherence to the shared client module for service interactions.
- Violation handling: Code modifications introducing unapproved direct network calls or alternative client implementations are rejected during review.
- Violation handling: Non-compliant commands must be refactored to consume the centralized client interface before merging.
- Exception process: Requests for exceptions must be submitted via architectural review with justification for bypassing standard client boundaries.
- Exception process: Approved exceptions require formal tracking and an identified roadmap for eventual alignment with the API client.
</enforcement>
