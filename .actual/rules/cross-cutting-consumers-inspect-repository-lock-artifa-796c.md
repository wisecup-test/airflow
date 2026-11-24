---
version: 2
name: cross-cutting-consumers-inspect-repository-lock-artifa-796c
description: "Airflow and Airflow SDK Library Integration: Consumers Inspect Repository Lock Artifact Resolve"
metadata:
  actual:
    adr_id: 796c94a0-33eb-4230-9a9a-29d1921854f1
    status: accepted
    candidate_group_id: c8078bf1-61a3-47de-bb54-fad03972f61c
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/docs/**
      - src/tests_common/test_utils/**
---

# Airflow and Airflow SDK Library Integration: Consumers Inspect Repository Lock Artifact Resolve

Scope: applies to `devel-common/src/docs/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Shared testing infrastructure and mock utilities modeling orchestration behavior.
- Documentation generation utilities inspecting provider packages and configuration metadata.
- Performance evaluation kits measuring orchestration memory and process resource utilization.

### Rules

- **R-LM-001** MUST: Consumers MUST inspect the repository lock artifact to resolve the exact locked version of the orchestration library before implementing or updating contract implementations.

### Verify

- Discover the repository test execution script and run the test suite targeting shared testing utilities.
- Discover the repository documentation build script and verify that provider documentation generation completes without configuration errors.

**Accept when:**

- All shared test harness suites pass verification against both core and SDK interfaces.
- Documentation generation scripts successfully resolve package metadata and generate documentation artifacts without contract errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites executing across all supported runtime matrix configurations.
- Verified by: Code review validation for any updates to shared test harnesses, mock managers, or documentation helpers.
- Violation handling: Pull requests introducing unencapsulated version checks or out-of-contract timetable implementations will be rejected by review and blocked by automated continuous integration failures.
- Exception process: Exceptions require approval from the core framework architecture maintainers accompanied by an issue tracking the migration plan.
</enforcement>
