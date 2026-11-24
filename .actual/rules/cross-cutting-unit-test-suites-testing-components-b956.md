---
version: 2
name: cross-cutting-unit-test-suites-testing-components-b956
description: "Adoption of github.com/apache/airflow/go-sdk/pkg/api Client Module: Unit Test Suites Testing Components Dependent"
metadata:
  actual:
    adr_id: b9566643-8a3f-42d5-bc7d-1e470e44bece
    status: accepted
    candidate_group_id: 525c4175-1045-421a-8fbf-e424a9e05796
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - pkg/api/mocks/**
      - sdk/**
---

# Adoption of github.com/apache/airflow/go-sdk/pkg/api Client Module: Unit Test Suites Testing Components Dependent

Scope: applies to `go-sdk/bundle/bundlev1/**`, `go-sdk/pkg/api/mocks/**`, `go-sdk/sdk/**`.

These rules are ALWAYS ACTIVE for:
- All internal SDK packages and workload execution bundles interacting with platform control plane resources.
- Unit testing suites simulating interactions with platform endpoints.

### Rules

- **R-LM-001** MUST: Unit test suites testing components dependent on API clients MUST utilize the provided mock client interfaces rather than establishing live network connections.

### Verify

- Discover the project build and test runner from the repository manifest and execute the test suite covering client and mock implementations.
- Discover the repository static analysis tools to verify that all service communications adhere to module interface contracts.

**Accept when:**

- All test suites covering API client implementations and mock assertions execute cleanly without errors.
- Static analysis confirms that service interactions exclusively route through client interfaces defined in the adopted module without unabstracted transport calls.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites executing mock verification tests.
- Verified by: Static architectural linting and dependency graph validation during pull request review.
- Violation handling: Pull requests introducing direct transport calls or bypassing API contracts will be rejected by automated CI checks.
- Violation handling: Violations detected in existing code must be refactored to consume the centralized client contracts prior to release tagging.
- Exception process: Exceptions require formal review by the SDK architectural review team.
- Exception process: Proposals must demonstrate why dedicated client interfaces in the adopted module cannot accommodate the proposed communication pattern.
</enforcement>
