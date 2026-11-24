---
version: 2
name: cross-cutting-decorated-functions-memoized-via-airflow-2b0e
description: "Standardization on airflow_breeze.utils.functools_cache for Function Memoization: Decorated Functions Memoized Via Airflow Breeze"
metadata:
  actual:
    adr_id: 2b0e94a9-a16c-4718-a53a-6615815f6324
    status: accepted
    candidate_group_id: 62a02ca6-f081-4f03-8875-4b7197f5fe67
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Standardization on airflow_breeze.utils.functools_cache for Function Memoization: Decorated Functions Memoized Via Airflow Breeze

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Utility modules and constant definition files within the developer tooling framework.
- Pure, deterministic functions processing configuration data, integration lists, or system environment lookups.

### Rules

- **R-LM-001** MUST_NOT: Decorated functions memoized via airflow_breeze.utils.functools_cache MUST NOT be applied to functions that produce side effects, mutate arguments in place, or rely on volatile runtime state.

### Verify

- Discover and execute the project test suite runner to validate that memoized functions return expected results.
- Discover and execute the project linter and static analysis scripts to verify imports and syntax compliance.

**Accept when:**

- The project test suite passes completely with zero caching regressions or mutated state failures.
- Static analysis and import verification succeed without reporting unauthorized caching decorators or duplicate implementations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test suite execution verifying cached function correctness and determinism.
- Verified by: Peer code review auditing all new usages of the memoization decorator across developer tooling modules.
- Violation handling: Pull requests introducing ad-hoc caching implementations or applying memoization to impure functions are blocked until corrected.
- Exception process: Exceptions for custom eviction policies require written architecture rationale in the pull request and maintainer approval.
</enforcement>
