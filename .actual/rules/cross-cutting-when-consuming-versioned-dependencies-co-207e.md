---
version: 2
name: cross-cutting-when-consuming-versioned-dependencies-co-207e
description: "performance_dag Internal Module Public Protocol Contracts: When Consuming Versioned Dependencies Consumers Inspect"
metadata:
  actual:
    adr_id: 207ef4c0-14e2-4867-a4ee-d35f7f69c6ad
    status: accepted
    candidate_group_id: 973b25d4-b808-44ac-97ff-9c14df951e29
    evidence:
      source: deep
  scope:
    root: performance
    paths:
      - src/performance_dags/performance_dag/**
---

# performance_dag Internal Module Public Protocol Contracts: When Consuming Versioned Dependencies Consumers Inspect

Scope: applies to `performance/src/performance_dags/performance_dag/**`.

These rules are ALWAYS ACTIVE for:
- Workflow definitions and testing modules generating synthetic performance benchmark DAGs.
- Public interfaces consuming performance DAG configuration dictionaries and runtime environment settings.

### Rules

- **R-PEA-001** MUST: When consuming versioned dependencies, consumers MUST inspect the repository lock or resolution artifact to determine the exact resolved dependency versions before implementation.

### Verify

- Discover and run the project test runner to execute unit tests covering performance DAG configuration validation and graph generation protocols.
- Discover and execute static analysis tools across the repository to verify conformance to public interface contracts.

**Accept when:**

- All unit tests verifying configuration validation contracts pass successfully without errors.
- Topology generation protocols construct valid acyclic task graphs for binary tree and grid configurations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites executing configuration validation tests.
- Verified by: Peer code review checking that benchmark graphs utilize standard public chaining interfaces.
- Violation handling: Pull requests bypassing configuration contracts or chaining protocols are blocked until updated.
- Violation handling: Violations discovered in active branches trigger test failures during workflow compilation.
- Exception process: Submit an architectural review request outlining custom topology requirements not supported by current chaining protocols.
</enforcement>
