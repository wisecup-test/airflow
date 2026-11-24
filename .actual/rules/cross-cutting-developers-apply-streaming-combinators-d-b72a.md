---
version: 2
name: cross-cutting-developers-apply-streaming-combinators-d-b72a
description: "Standard Library itertools Adoption for Sequence and Stream Processing: Developers Apply Streaming Combinators Directly Input"
metadata:
  actual:
    adr_id: b72a9c41-1666-45df-82c2-cb35d6ef7bc5
    status: accepted
    candidate_group_id: 7fefcef0-b2d4-4c12-ba68-d265767c9102
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/docs_build/**
      - src/tests_common/**
---

# Standard Library itertools Adoption for Sequence and Stream Processing: Developers Apply Streaming Combinators Directly Input

Scope: applies to `devel-common/src/sphinx_exts/docs_build/**`, `devel-common/src/tests_common/**`.

These rules are ALWAYS ACTIVE for:
- Shared testing utilities, developer extensions, and test harness helper modules processing sequential or stream data.
- Internal modules handling log parsing, batch record capture, and inventory retrieval pipelines.

### Rules

- **R-LM-001** MUST: Developers MUST apply streaming combinators directly to input iterators to maintain lazy evaluation guarantees throughout transformation pipelines.

### Verify

- Discover and run the project test runner configured in the repository dependency manifest to execute the unit and integration test suites.
- Discover and run the repository code formatting and static analysis scripts to verify adherence to style rules and import conventions.

**Accept when:**

- All unit and integration tests across developer utilities pass without memory exhaustion or iterator re-consumption exceptions.
- Static analysis checks pass with zero warnings regarding unused imports or invalid sequence operations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test execution
- Verified by: Peer code review for all pull requests modifying shared utilities and test harnesses
- Violation handling: Pull requests exhibiting manual list accumulation for stream processing are requested for refactoring during code review.
- Violation handling: Static analysis and test failures block merging into main integration branches.
- Exception process: Exceptions must be justified in pull request descriptions citing performance or architectural constraints.
- Exception process: Approval required from designated codebase maintainers.
</enforcement>
