---
version: 2
name: cross-cutting-consumers-inspect-repository-dependency-9e52
description: "Adoption of tests_common.test_utils.version_compat for Shared Test Compatibility Abstraction: Consumers Inspect Repository Dependency Resolution Artifact"
metadata:
  actual:
    adr_id: 9e529925-e552-402b-ad62-ab6c09090949
    status: accepted
    candidate_group_id: 0994bdd3-ef77-4230-9922-fbecab2a94e6
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Adoption of tests_common.test_utils.version_compat for Shared Test Compatibility Abstraction: Consumers Inspect Repository Dependency Resolution Artifact

Scope: applies to `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Shared testing harnesses, mock operators, test doubles, and test fixtures defined in common test utilities.
- Test components that handle deprecation warnings or version-divergent architectural subsystems.

### Rules

- **R-LM-001** MUST: Consumers MUST inspect the repository dependency resolution artifact to determine the authoritative locked version of all relevant runtime modules before implementing compatibility adapters.

### Verify

- Discover the project test execution runner and run the test suite targeting shared testing utilities.
- Discover the repository static analysis and type checking utilities to verify compatibility interface contracts.

**Accept when:**

- All shared test utilities and fixtures execute without errors across all configured environment matrix variants.
- No direct conditional version branching exists outside the centralized compatibility module.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration matrix runs validating test execution across supported target environments.
- Verified by: Peer code review ensuring new test helpers import compatibility shims rather than implementing inline checks.
- Violation handling: Pull requests introducing direct inline version branching in test helpers will be blocked during code review.
- Exception process: Exceptions must be submitted via architectural review request and documented with rationale in test module headers.
</enforcement>
