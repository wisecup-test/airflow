---
version: 2
name: cross-cutting-command-reproduction-builders-isolate-pa-ca2b
description: "shlex Module Adoption for Shell Command Lexical Parsing and Tokenization: Command Reproduction Builders Isolate Parameter Extraction"
metadata:
  actual:
    adr_id: ca2b4016-2459-4320-b580-e718329b4f9f
    status: accepted
    candidate_group_id: b5e85b92-616b-4ef8-a94b-03413a178942
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# shlex Module Adoption for Shell Command Lexical Parsing and Tokenization: Command Reproduction Builders Isolate Parameter Extraction

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Developer tooling utilities responsible for building, reproducing, or executing command-line invocations.
- Modules handling command string tokenization and argument escaping.

### Rules

- **R-LM-001** SHOULD: Command reproduction builders SHOULD isolate parameter extraction from argument formatting by normalizing command tokens into structured collections before lexical assembly.

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
