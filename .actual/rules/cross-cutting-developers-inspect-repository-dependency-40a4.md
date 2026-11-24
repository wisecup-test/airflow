---
version: 2
name: cross-cutting-developers-inspect-repository-dependency-40a4
description: "Adoption of shutil for High-Level Filesystem and Directory Management: Developers Inspect Repository Dependency Manifest Authoritative"
metadata:
  actual:
    adr_id: 40a45e64-fdc5-46c3-9788-55b66164cad9
    status: accepted
    candidate_group_id: 1df8a3a7-4e7d-4f55-ba1f-7f3faa35334b
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Adoption of shutil for High-Level Filesystem and Directory Management: Developers Inspect Repository Dependency Manifest Authoritative

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Development tooling, documentation generation scripts, and test infrastructure modules performing filesystem directory creation, relocation, and deletion.
- Lifecycle cleanup fixtures and artifact archiving routines in test harnesses.

### Rules

- **R-LM-001** MUST: Developers MUST inspect the repository dependency manifest and authoritative lock artifact to verify the runtime environment version compatibility prior to modifying filesystem-dependent utilities.

### Verify

- Discover the project test execution runner from the build manifest and execute the test suite covering build and test utility modules.
- Discover the code quality and static analysis runner configured in the repository and verify that all filesystem operations conform to linting and style rules.

**Accept when:**

- All automated test suites for development tooling and test fixtures pass cleanly with zero directory manipulation errors.
- Static analysis checks confirm that high-level directory cleanup and relocation routines use shutil rather than shell subprocess invocations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline checks and test suite executions.
- Verified by: Peer code review for all modifications touching development tooling, documentation generation, and testing harness utilities.
- Violation handling: Pull requests containing platform-specific shell subprocess calls for filesystem management are flagged during review and blocked until converted to shutil operations.
- Violation handling: Continuous integration test failures caused by unhandled filesystem manipulation exceptions must be resolved prior to merging.
- Exception process: Exceptions require approval from the engineering team lead and documented justification explaining why platform-native or external process invocation is mandatory.
</enforcement>
