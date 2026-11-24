---
version: 2
name: cross-cutting-consumers-verify-repository-lock-resolut-651f
description: "airflow.models Core Domain Modeling: Consumers Verify Repository Lock Resolution Artifact"
metadata:
  actual:
    adr_id: 651f4933-14f7-4e91-8f01-0c4ef0e1869b
    status: accepted
    candidate_group_id: 79d5e955-c5e1-4253-94ae-0027687a0c08
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# airflow.models Core Domain Modeling: Consumers Verify Repository Lock Resolution Artifact

Scope: applies to `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Shared test utility packages, system connection helpers, and mock hook implementations interacting with orchestration connections and execution records.
- Integration test fixtures validating task execution and credential provisioning.

### Rules

- **R-LM-001** MUST: Consumers MUST verify the repository lock or resolution artifact to confirm the exact resolved version of airflow.models before implementing or updating model integrations.

### Verify

- Discover and execute the project integration test suite targeting shared test utilities and connection helpers.
- Discover and run the project static type checker to verify model constructor arguments and hook inheritance compatibility.

**Accept when:**

- All test helper modules instantiate connections using airflow.models.Connection without parameter type errors.
- Integration test suites using MockedHook and connection provisioning helpers pass without schema discrepancies.
- Static type inspection confirms compliance with core model attributes and exception types.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites executing integration and system helper tests.
- Verified by: Static type verification and lint analysis in pull request validation pipelines.
- Verified by: Peer code review verifying adherence to canonical model usage.
- Violation handling: Pull requests introducing raw dictionary connection mocks or bypassed entity structures are blocked by automated review gates.
- Violation handling: Violations must be refactored to consume canonical model abstractions before code merge.
- Exception process: Submit an architectural exception request detailing why canonical domain models cannot be utilized in the specific test scenario.
- Exception process: Obtain approval from the architecture review board or project maintainers prior to merging an exception.
</enforcement>
