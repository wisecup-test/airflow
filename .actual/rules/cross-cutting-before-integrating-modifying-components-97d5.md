---
version: 2
name: cross-cutting-before-integrating-modifying-components-97d5
description: "Standardization on airflow.exceptions for Domain Error and Lifecycle Signaling: Before Integrating Modifying Components Dependent Versioned"
metadata:
  actual:
    adr_id: 97d513b6-ab6a-4883-b45c-57edc9b25915
    status: accepted
    candidate_group_id: 20fa7624-49de-4fba-9998-b7c59024f5cb
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Standardization on airflow.exceptions for Domain Error and Lifecycle Signaling: Before Integrating Modifying Components Dependent Versioned

Scope: applies to `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Shared utility modules, test helpers, connector setup fixtures, and execution wrappers interacting with framework orchestration.
- Error reporting, command execution monitoring, and task state propagation boundaries.

### Rules

- **R-LM-001** MUST: Before integrating or modifying components dependent on versioned dependencies, the consumer MUST inspect the repository lock or resolution artifact to determine the exact resolved dependency version and confirm API availability against version-specific documentation.

### Verify

- find . -maxdepth 3 -type f -name "*test*" -exec echo "Running verification suite: {}" \;
- find . -maxdepth 3 -type f -name "*lint*" -exec echo "Running compliance analysis: {}" \;

**Accept when:**

- All command execution, connection helper, and task watcher modules raise AirflowException on operational failure conditions.
- Automated test suites pass with zero unhandled standard library runtime exceptions in framework utility boundaries.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites and static analysis gates.
- Verified by: Peer review during pull request evaluation.
- Violation handling: Pull requests introducing raw unhandled system exceptions in framework utility boundaries are rejected.
- Violation handling: Static analysis violations block deployment until compliant exception classes are utilized.
- Exception process: Submit an architectural review request explaining why the framework exception hierarchy cannot be imported or raised.
- Exception process: Document the approved non-standard exception boundary and rationale in module documentation.
</enforcement>
