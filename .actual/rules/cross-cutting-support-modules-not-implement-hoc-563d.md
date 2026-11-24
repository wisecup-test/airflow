---
version: 2
name: cross-cutting-support-modules-not-implement-hoc-563d
description: "Airflow and Airflow SDK Library Integration: Support Modules Not Implement Hoc Scheduling"
metadata:
  actual:
    adr_id: 563df5e2-12d9-4f12-a890-ca6799e765a4
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

# Airflow and Airflow SDK Library Integration: Support Modules Not Implement Hoc Scheduling

Scope: applies to `devel-common/src/docs/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Shared testing infrastructure and mock utilities modeling orchestration behavior.
- Documentation generation utilities inspecting provider packages and configuration metadata.
- Performance evaluation kits measuring orchestration memory and process resource utilization.

### Rules

- **R-LM-001** MUST_NOT: Support modules MUST NOT implement ad-hoc scheduling models that bypass the Timetable contract specifications defined by the orchestration framework.

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
