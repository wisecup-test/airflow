---
version: 2
name: cross-cutting-tests-common-test-utils-shared-4248
description: "Internal Module Adoption: tests_common.test_utils Shared Test Harnesses: Consumers Discover Repository Lock File Verify"
metadata:
  actual:
    adr_id: 4248c67c-6533-45e1-8ca1-187f6c4565f4
    status: accepted
    candidate_group_id: 6ac0f4e0-f518-443b-a470-f85df7b3e26e
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Internal Module Adoption: tests_common.test_utils Shared Test Harnesses: Consumers Discover Repository Lock File Verify

Scope: applies to `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- All unit and integration test suites within the repository verifying task execution, hook connectivity, and plugin management.
- Internal test utility modules and testing support packages.

### Rules

- **R-LM-001** MUST: Consumers MUST discover the repository lock file and verify resolved dependency versions before implementing or executing test harnesses.

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
