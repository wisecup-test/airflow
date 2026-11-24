---
version: 2
name: cross-cutting-engineers-discover-project-dependency-ma-0fe3
description: "shlex Module Adoption for Shell Command Lexical Parsing and Tokenization: Engineers Discover Project Dependency Manifest Resolve"
metadata:
  actual:
    adr_id: 0fe35d7b-3ad7-4106-9109-6f9834bdcece
    status: accepted
    candidate_group_id: b5e85b92-616b-4ef8-a94b-03413a178942
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# shlex Module Adoption for Shell Command Lexical Parsing and Tokenization: Engineers Discover Project Dependency Manifest Resolve

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Developer tooling utilities responsible for building, reproducing, or executing command-line invocations.
- Modules handling command string tokenization and argument escaping.

### Rules

- **R-LM-001** MUST: Engineers MUST discover the project dependency manifest and resolve locked dependency specifications prior to introducing or modifying module integrations.

### Verify

- Discover the project test runner and execute unit tests targeting developer command utilities and argument tokenization modules.
- Discover the repository static analysis linter and run static checks across tooling packages to verify proper lexical parsing usage.

**Accept when:**

- All unit tests covering command construction, reproduction, and execution pass with zero failures.
- Static analysis validates that no unescaped string concatenation or manual whitespace splitting occurs in command composition modules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites validating command construction and lexical tokenization behavior.
- Verified by: Peer code review verification on all pull requests modifying command composition utilities.
- Violation handling: Automated test failures block integration of code introducing unescaped command composition.
- Violation handling: Pull requests containing manual whitespace splitting or raw string concatenation for shell arguments must be revised before merge.
- Exception process: Submit an architectural exception request documenting why shell lexical parsing is inapplicable for the specific command pipeline, subject to maintainer approval.
</enforcement>
