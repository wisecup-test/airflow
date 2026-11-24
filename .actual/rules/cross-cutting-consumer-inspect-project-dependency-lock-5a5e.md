---
version: 2
name: cross-cutting-consumer-inspect-project-dependency-lock-5a5e
description: "Adoption of shlex for Command Argument Quoting and Process Execution Logging: Consumer Inspect Project Dependency Lock Artifact"
metadata:
  actual:
    adr_id: 5a5e7296-0e30-4551-9da5-3c7861d63e0d
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

# Adoption of shlex for Command Argument Quoting and Process Execution Logging: Consumer Inspect Project Dependency Lock Artifact

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Command execution wrappers, test helpers, and build extensions that log or format process argument sequences.
- Diagnostic and error reporting components displaying subprocess command strings.

### Rules

- **R-LM-001** MUST: The consumer MUST inspect the project dependency lock artifact to verify the runtime environment and standard library specifications before implementing command execution modules.

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
