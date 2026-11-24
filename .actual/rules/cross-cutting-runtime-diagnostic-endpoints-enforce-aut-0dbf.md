---
version: 2
name: cross-cutting-runtime-diagnostic-endpoints-enforce-aut-0dbf
description: "Connection Hook test_connection Verification Protocol and Dynamic Diagnostic Interfaces: Runtime Diagnostic Endpoints Enforce Authorization Boundaries"
metadata:
  actual:
    adr_id: 0dbfd638-8906-44be-88d3-9192b233c6ae
    status: accepted
    candidate_group_id: 9440aa7d-0ed7-4846-8c56-526b8610bc1e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Connection Hook test_connection Verification Protocol and Dynamic Diagnostic Interfaces: Runtime Diagnostic Endpoints Enforce Authorization Boundaries

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Connection data models and external hook integrations supporting live connection testing.
- Core API endpoints exposing synchronous or asynchronous external credential validation.
- Workflow task definitions performing live runtime assertions in example workflows.

### Rules

- **R-TS-001** MUST: Runtime diagnostic API endpoints MUST enforce authorization boundaries and respect administrative configuration flags before dispatching connectivity tests to external hosts.

### Verify

- Discover and execute the project automated test suite via the repository configuration to ensure all tests pass.
- Discover and run static type checking across API and model directories to verify diagnostic method contract compliance.

**Accept when:**

- Project automated test suites execute cleanly with zero test failures.
- Runtime connectivity test interfaces return structured status indicators without exposing unhandled client exceptions to callers.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis rules verifying that runtime diagnostic endpoints remain isolated from unit testing suites.
- Verified by: Continuous integration test runners executing standard project test suites.
- Verified by: Peer code review for all new hook implementations and connection route definitions.
- Violation handling: Code introducing test runner dependencies into runtime models or API boundaries must be rejected in pull request review.
- Violation handling: Hook implementations that fail to return structured diagnostic status from validation methods must be refactored before merging.
- Exception process: Exceptions to runtime verification protocols must be reviewed by the architecture review board and documented in the integration design record.
- Exception process: Temporary bypass of diagnostic endpoints requires explicit configuration toggles in the core configuration registry.
</enforcement>
