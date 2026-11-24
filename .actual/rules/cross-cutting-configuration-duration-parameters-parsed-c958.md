---
version: 2
name: cross-cutting-configuration-duration-parameters-parsed-c958
description: "performance_dag Internal Module Public Protocol Contracts: Configuration Duration Parameters Parsed Regular Expression"
metadata:
  actual:
    adr_id: c958cc06-aa19-4d94-9a14-e493097f085c
    status: accepted
    candidate_group_id: 973b25d4-b808-44ac-97ff-9c14df951e29
    evidence:
      source: deep
  scope:
    root: performance
    paths:
      - src/performance_dags/performance_dag/**
---

# performance_dag Internal Module Public Protocol Contracts: Configuration Duration Parameters Parsed Regular Expression

Scope: applies to `performance/src/performance_dags/performance_dag/**`.

These rules are ALWAYS ACTIVE for:
- Workflow definitions and testing modules generating synthetic performance benchmark DAGs.
- Public interfaces consuming performance DAG configuration dictionaries and runtime environment settings.

### Rules

- **R-PEA-001** MUST: Configuration duration parameters MUST be parsed using regular expression duration protocols and validated using check_positive and convertibility contracts.

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
