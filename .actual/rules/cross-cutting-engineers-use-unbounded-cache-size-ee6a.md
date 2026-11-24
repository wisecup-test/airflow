---
version: 2
name: cross-cutting-engineers-use-unbounded-cache-size-ee6a
description: "Adoption of methodtools for Method and Classmethod Memoization: Engineers Use Unbounded Cache Size Class"
metadata:
  actual:
    adr_id: ee6a59ce-2575-4526-bf92-70fb3e6235fd
    status: accepted
    candidate_group_id: b720d11a-8a18-4088-b4b5-724d680200f7
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of methodtools for Method and Classmethod Memoization: Engineers Use Unbounded Cache Size Class

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Instance methods and class methods on serialization nodes, graph components, and task configuration classes that require memoized property calculations.

### Rules

- **R-LM-001** MAY: Engineers MAY use an unbounded cache size on class methods where the return set represents an immutable domain enumeration.

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
