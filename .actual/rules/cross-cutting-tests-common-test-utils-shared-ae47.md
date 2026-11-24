---
version: 2
name: cross-cutting-tests-common-test-utils-shared-ae47
description: "Internal Module Adoption: tests_common.test_utils Shared Test Harnesses: Project Adopt Internal Module Tests Common"
metadata:
  actual:
    adr_id: ae4761cd-d7c5-41fb-bf27-27f565281d8f
    status: accepted
    candidate_group_id: 6ac0f4e0-f518-443b-a470-f85df7b3e26e
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Internal Module Adoption: tests_common.test_utils Shared Test Harnesses: Project Adopt Internal Module Tests Common

Scope: applies to `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- All unit and integration test suites within the repository verifying task execution, hook connectivity, and plugin management.
- Internal test utility modules and testing support packages.

### Rules

- **R-LM-001** MUST: The project MUST adopt the internal module tests_common.test_utils as the centralized source for shared test harnesses, mock task instances, database hook doubles, and plugin manager patches.

### Verify

- "${PROJECT_TEST_RUNNER}" discover --pattern="test_utils*"
- "${PROJECT_VERIFY_SCRIPT}"

**Accept when:**

- The shared testing module suite executes successfully with zero failures.
- All test double interfaces adhere to upstream core runtime and SDK class contracts.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites.
- Verified by: Peer code review for pull requests modifying or adding test harnesses.
- Violation handling: Pull requests introducing ad-hoc mocks where shared test doubles exist in tests_common.test_utils will be blocked.
- Violation handling: Engineers must refactor redundant test doubles to utilize the shared module.
- Exception process: Submit an architectural review request outlining why existing shared test utilities cannot accommodate the testing requirement.
</enforcement>
