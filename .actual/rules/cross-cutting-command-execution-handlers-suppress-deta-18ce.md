---
version: 2
name: cross-cutting-command-execution-handlers-suppress-deta-18ce
description: "Adoption of shlex for Command Argument Quoting and Process Execution Logging: Command Execution Handlers Suppress Detailed Shlex"
metadata:
  actual:
    adr_id: 18ce0c55-5ac6-435c-b11e-61e81e4ec913
    status: accepted
    candidate_group_id: 20470fc4-249d-4849-9005-87c1c833140c
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Adoption of shlex for Command Argument Quoting and Process Execution Logging: Command Execution Handlers Suppress Detailed Shlex

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Command execution wrappers, test helpers, and build extensions that log or format process argument sequences.
- Diagnostic and error reporting components displaying subprocess command strings.

### Rules

- **R-LM-001** MAY: Command execution handlers MAY suppress detailed shlex.quote command logging in execution modes explicitly configured for silent execution.

### Verify

- # Discover repository test runner from manifest and execute test suites targeting command execution utilities
- # Run repository static analysis to verify uniform usage of shlex.quote across command logging modules

**Accept when:**

- All test suites exercising command execution and logging wrappers execute with zero failures.
- Static analysis confirms all command string formatting routines invoke shlex.quote on argument elements.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites validating command executor logging and error message generation.
- Verified by: Code reviews checking that all external process logging utilizes standard argument quoting.
- Violation handling: Pull requests introducing unquoted command string formatting in logging or error handlers are blocked during review.
- Violation handling: Static analysis failures on unescaped command logging must be remediated prior to merge.
- Exception process: Exceptions for non-standard command formatting require documented approval from the architecture team detailing why standard POSIX quoting is unsuitable.
</enforcement>
