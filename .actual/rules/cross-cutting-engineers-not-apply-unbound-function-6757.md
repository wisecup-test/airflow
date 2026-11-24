---
version: 2
name: cross-cutting-engineers-not-apply-unbound-function-6757
description: "Adoption of methodtools for Method and Classmethod Memoization: Engineers Not Apply Unbound Function Level"
metadata:
  actual:
    adr_id: 67574cb4-152e-4a9b-a5ba-1dfbabe38d4c
    status: accepted
    candidate_group_id: b720d11a-8a18-4088-b4b5-724d680200f7
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of methodtools for Method and Classmethod Memoization: Engineers Not Apply Unbound Function Level

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Instance methods and class methods on serialization nodes, graph components, and task configuration classes that require memoized property calculations.

### Rules

- **R-LM-001** MUST_NOT: Engineers MUST NOT apply unbound function-level caching decorators to instance methods where the lifecycle of the instance must remain eligible for garbage collection.

### Verify

- Discover the repository test runner script and execute unit tests targeting serialization and task modules.
- Discover the repository static analysis and linting scripts to verify adherence to method caching conventions.

**Accept when:**

- All unit tests covering serialization and task node caching pass without regression.
- Static analysis checks confirm no standard library function caches decorate bound instance methods.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test suites verifying serialization and task execution behavior.
- Verified by: Automated static analysis checks and peer reviews of proposed caching decorators.
- Violation handling: Pull requests applying standard function caching decorators to bound instance methods will be rejected until updated to the required decorator.
- Exception process: Exceptions require approval from the technical architecture group with memory profiling data demonstrating no reference retention leaks.
</enforcement>
