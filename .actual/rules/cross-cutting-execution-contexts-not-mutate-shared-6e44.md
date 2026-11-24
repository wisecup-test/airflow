---
version: 2
name: cross-cutting-execution-contexts-not-mutate-shared-6e44
description: "copy Module for Object Duplication and State Isolation: Execution Contexts Not Mutate Shared Task"
metadata:
  actual:
    adr_id: 6e447604-02aa-42f5-b756-ef792e4632da
    status: accepted
    candidate_group_id: 1f54dd98-8d8d-4a73-bd07-545b1bdb268f
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# copy Module for Object Duplication and State Isolation: Execution Contexts Not Mutate Shared Task

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Test execution utilities, runtime helpers, and documentation cross-reference resolvers requiring object duplication.

### Rules

- **R-LM-001** MUST_NOT: Execution contexts MUST_NOT mutate shared task instance or operational state without creating an independent copy.

### Verify

- "${PROJECT_TEST_RUNNER:-true}"
- "${VERIFY_SUITE_COMMAND:-true}"

**Accept when:**

- All discovered test suites execute successfully without state contamination across test cases.
- Cloned instances retain independent attribute mappings when underlying dictionaries are modified.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites.
- Verified by: Peer code review focusing on state duplication and mutation boundaries.
- Violation handling: Pull requests mutating shared runtime or test structures without explicit duplication are blocked during code review.
- Violation handling: State contamination failures in test suites trigger immediate review of object duplication logic.
- Exception process: Exceptions require approval from principal engineering leads with documented rationale demonstrating that the mutated object is strictly single-use.
</enforcement>
