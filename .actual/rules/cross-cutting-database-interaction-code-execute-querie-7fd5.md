---
version: 2
name: cross-cutting-database-interaction-code-execute-querie-7fd5
description: "SQLAlchemy ORM Session Statement Execution Pattern: Database Interaction Code Execute Queries Operations"
metadata:
  actual:
    adr_id: 7fd53e72-b8e1-44d9-a016-7b836b15a4eb
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

# SQLAlchemy ORM Session Statement Execution Pattern: Database Interaction Code Execute Queries Operations

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/**`.

These rules are ALWAYS ACTIVE for:
- All data access utilities, persistence test harnesses, and model query components interacting with relational database entities.
- Components managing database lifecycle, entity assertions, or test state cleanup.

### Rules

- **R-DAP-001** MUST: Database interaction code MUST execute database queries and operations using SQLAlchemy ORM statement execution methods via session execute or session scalars rather than legacy query building constructs.

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
