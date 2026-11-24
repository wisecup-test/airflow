---
version: 2
name: cross-cutting-data-access-routines-not-perform-deac
description: "SQLAlchemy ORM Session Statement Execution Pattern: Data Access Routines Not Perform Direct"
metadata:
  actual:
    adr_id: deaca7b2-9db6-4821-b7dc-cb1e6eea7a35
    status: accepted
    candidate_group_id: 8638214a-0b46-499d-85d0-dd50dc7c2568
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/**
---

# SQLAlchemy ORM Session Statement Execution Pattern: Data Access Routines Not Perform Direct

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/**`.

These rules are ALWAYS ACTIVE for:
- All data access utilities, persistence test harnesses, and model query components interacting with relational database entities.
- Components managing database lifecycle, entity assertions, or test state cleanup.

### Rules

- **R-DAP-001** SHOULD_NOT: Data access routines SHOULD_NOT perform direct unparameterized query assembly or bypass the session abstraction when interacting with mapped entities.

### Verify

- discover-test-runner --suite data-access --run
- discover-static-analyzer --target persistence-modules --check

**Accept when:**

- All data access operations and database test suites execute cleanly with zero session leak warnings.
- Static analysis validates that all database interactions route through session statement execution and unit-of-work persistence methods.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suite execution in continuous integration pipelines.
- Verified by: Peer code review verifying adherence to session execution standards and absence of legacy query mechanisms.
- Violation handling: Pull requests containing unapproved data access patterns or direct raw query executions will be blocked from merging until refactored.
- Exception process: Exceptions for performance-critical raw queries must be reviewed and approved by database architecture leads with documented justification.
</enforcement>
