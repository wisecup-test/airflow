---
version: 2
name: cross-cutting-command-executors-encapsulate-execution-c2dd
description: "Subprocess Module Adoption for External Process Execution: Command Executors Encapsulate Execution Logic Within"
metadata:
  actual:
    adr_id: c2dd5263-4e9f-4891-ac5b-688d2cbe8fc2
    status: accepted
    candidate_group_id: f6e020fc-2d38-4848-89c2-11762cf6fe79
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Subprocess Module Adoption for External Process Execution: Command Executors Encapsulate Execution Logic Within

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Subprocess and external process invocation logic within shared tooling and test utility modules.
- Diagnostic command runners requiring standard stream redirection and structured logging.

### Rules

- **R-LM-001** SHOULD: Command executors SHOULD encapsulate execution logic within shared utility classes inheriting from common logging mixins to provide unified diagnostic output.

### Verify

- Discover and run the project test suite using the configured test runner in the repository.
- Discover and execute the repository static analysis and linting checks to verify subprocess usage compliance.

**Accept when:**

- All unit and integration tests executing external processes pass without unhandled exceptions.
- Static analysis validates that all subprocess invocations use argument sequences without shell execution flags.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test execution and automated static analysis.
- Verified by: Peer code review for all tooling and utility modifications.
- Violation handling: Pull requests containing unquoted or raw shell subprocess invocations are blocked until refactored to standard executors.
- Violation handling: Continuous integration failures on unexpected return codes require remediation before merging.
- Exception process: Submit an architectural review request detailing why structured subprocess execution is infeasible, accompanied by security sign-off.
</enforcement>
