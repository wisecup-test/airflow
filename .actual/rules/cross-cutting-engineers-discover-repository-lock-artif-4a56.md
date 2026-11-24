---
version: 2
name: cross-cutting-engineers-discover-repository-lock-artif-4a56
description: "Adoption of methodtools for Method and Classmethod Memoization: Engineers Discover Repository Lock Artifact Verify"
metadata:
  actual:
    adr_id: 4a56dfa0-6b07-4a96-bf18-4833f20038aa
    status: accepted
    candidate_group_id: b720d11a-8a18-4088-b4b5-724d680200f7
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of methodtools for Method and Classmethod Memoization: Engineers Discover Repository Lock Artifact Verify

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Instance methods and class methods on serialization nodes, graph components, and task configuration classes that require memoized property calculations.

### Rules

- **R-LM-001** MUST: Engineers MUST discover the repository lock artifact and verify the exact resolved version of the methodtools library prior to introducing or updating caching decorators.

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
