---
version: 2
name: cross-cutting-integration-verification-suites-establis-a5bb
description: "airflowctl.api.client Client Module Integration: Integration Verification Suites Established Validate Client"
metadata:
  actual:
    adr_id: a5bbc111-ab93-43d9-8b1b-7ed4b77b5e09
    status: accepted
    candidate_group_id: 5e00389a-d966-4155-8b68-86523b1752d8
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# airflowctl.api.client Client Module Integration: Integration Verification Suites Established Validate Client

Scope: applies to `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- Client-side communication modules interfacing with backend API services
- CLI command modules handling external configuration, credentials, and payload transformation

### Rules

- **R-TS-001** SHOULD: Integration verification suites SHOULD be established to validate client request serializations against server endpoint specifications in automated verification pipelines.

### Verify

- Discover and run the project static analysis and linting suite from repository configuration files.
- Discover and execute the automated test runner targeting integration and client unit test specifications.

**Accept when:**

- All client operations route through the centralized API client module without direct unmanaged HTTP requests.
- Automated test suites pass without unhandled client connection or response deserialization errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline executing static analysis and test runner suites.
- Verified by: Peer review verifying adherence to centralized client calling conventions and error handling patterns.
- Violation handling: Merge requests containing raw HTTP calls or unhandled network exceptions will be blocked pending refactoring.
- Exception process: Exceptions for diagnostic or emergency bypass commands require approval from the lead architect and must be documented in code comments.
</enforcement>
