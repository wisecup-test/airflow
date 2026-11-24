---
version: 2
name: cross-cutting-before-writing-executing-code-that-879e
description: "Dynamic Module Loading and Lazy Resolution via importlib: Before Writing Executing Code That Imports"
metadata:
  actual:
    adr_id: 879e7481-1999-4fd6-93c0-11014bbb7ef2
    status: accepted
    candidate_group_id: 8cdd2f7d-87b1-48fb-a69c-19765ef8e67a
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Dynamic Module Loading and Lazy Resolution via importlib: Before Writing Executing Code That Imports

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All core subsystems executing dynamic module loading, pluggable component dispatch, or runtime user code ingestion.
- Subsystems implementing lazy attribute redirection and backward compatibility deprecation warnings.

### Rules

- **R-LM-001** MUST: Before writing or executing code that imports dependencies dynamically, developers MUST inspect the repository lock or resolution artifact to determine the exact resolved dependency versions and verify API compatibility against official reference documentation.

### Verify

- git grep -n -E 'importlib\\.(import_module|machinery|util)'
- git grep -n 'SourceFileLoader'
- git grep -n 'exec_module'

**Accept when:**

- All dynamic module loading routines utilize importlib APIs and pass automated test suites for module isolation.
- Global module cache state is verified to be cleanly managed and restored during module loading failures.
- Dynamic attribute redirection correctly triggers documented deprecation warnings without unhandled attribute errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Static analysis linting rules verifying importlib usage patterns across core subsystems.
- Verified by: Automated integration test suites validating dynamic module isolation and exception handling.
- Verified by: Peer review on pull requests touching runtime loading, serialization, or plugin dispatch mechanisms.
- Violation handling: Automated pull request checks fail on non-standard dynamic loading or unmanaged module cache manipulation.
- Violation handling: Code revisions required to align dynamic import structures with canonical importlib patterns prior to merge.
- Exception process: Submit an architecture exception request detailing why standard importlib specifications cannot satisfy the runtime requirement.
- Exception process: Secure explicit approval from the core framework architecture review group before introducing non-standard loading mechanisms.
</enforcement>
