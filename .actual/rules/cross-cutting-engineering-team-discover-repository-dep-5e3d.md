---
version: 2
name: cross-cutting-engineering-team-discover-repository-dep-5e3d
description: "Pytest System Test Harness and Fixture Architecture: Engineering Team Discover Repository Dependency Manifest"
metadata:
  actual:
    adr_id: 5e3d5bfe-d0c4-4626-9941-9213a7a54551
    status: accepted
    candidate_group_id: 3b69d00e-c8ab-4633-be37-6ffb93fb9efd
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Pytest System Test Harness and Fixture Architecture: Engineering Team Discover Repository Dependency Manifest

Scope: applies to `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- All system, performance, and external integration test suites.
- Shared test utility modules providing harness functions, fixtures, or connection providers.

### Rules

- **R-TS-001** MUST: The engineering team MUST discover the repository dependency manifest and lock artifact to resolve the exact locked version of the testing framework before authoring or modifying test suites.

### Verify

- Discover the test runner from the project configuration and execute tests matching the system marker.
- Discover and run the project static analysis and linting scripts to verify adherence to fixture scoping rules.
- Execute the test discovery command to ensure all system tests are properly registered under the test harness.

**Accept when:**

- All tests marked with system tags execute to completion with clean post-run state verification.
- Test execution produces zero unhandled resource warnings or uncleared filesystem artifacts.
- All deprecation warnings captured by warning fixtures match declared criteria.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suite pipelines executing marker-filtered test runs.
- Verified by: Code review checks validating fixture usage and marker annotations on new test cases.
- Violation handling: Pull requests containing untagged system tests or unmanaged state fixtures will fail automated verification and be blocked from merging.
- Violation handling: Tests causing environment contamination will be disabled until fixture teardown is corrected.
- Exception process: Exceptions require approval from the Testing Architecture Lead documented in the test module docstring.
</enforcement>
