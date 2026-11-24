---
version: 2
name: cross-cutting-state-transitions-scheduling-delays-reco-ca46
description: "Centralized Dimensional Metrics Instrumentation with stats Interface: State Transitions Scheduling Delays Recorded Timing"
metadata:
  actual:
    adr_id: ca46d89f-45aa-4a3f-ba2c-40172cb5a36f
    status: accepted
    candidate_group_id: 25b5d5ad-9c39-4b99-bfe1-8617e1f91e73
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Centralized Dimensional Metrics Instrumentation with stats Interface: State Transitions Scheduling Delays Recorded Timing

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All core scheduling processes, job execution runners, background processors, and domain state models.
- Telemetry emission involving heartbeats, task duration, scheduling delays, failure counters, and cache operations.

### Rules

- **R-MM-001** SHOULD: State transitions and scheduling delays SHOULD be recorded using timing measurements tagged with operational context such as queue identifiers and execution state.

### Verify

- Discover the repository's test runner and execute the metrics test suite to verify counter and timing emissions.
- Discover the static analysis and linting configuration to verify that metric call sites adhere to centralized abstraction constraints.
- Run unit validation on metric tag sanitization logic to ensure non-primitive attributes and null tags are handled safely.

**Accept when:**

- All operational and lifecycle instrumentation calls route exclusively through the centralized metrics interface without importing or invoking downstream metric collector clients directly.
- All metric tag payloads pass through tag pruning and serialization sanitization to prevent unhashable complex types and null tags from reaching telemetry backends.
- Verification test suites validate counter increments, timers, and tag schema conformance without raising schema or protocol serialization exceptions.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis rules verifying that operational components route telemetry exclusively through the centralized metrics abstraction.
- Verified by: Unit and integration test suites validating metric emission counts, timer execution, and tag sanitization behavior.
- Verified by: Peer review checks during code integration ensuring metric naming conventions conform to taxonomy guidelines.
- Violation handling: Pull requests introducing direct metric client invocations or unpruned tag payloads will fail automated compliance checks.
- Violation handling: Flagged violations require refactoring call sites to use standard metrics methods and sanitized tag structures prior to merge approval.
- Exception process: Submit an architecture exception request detailing why the centralized metrics interface cannot fulfill the operational telemetry requirement.
- Exception process: Secure approval from the observability and core platform architecture maintainers before committing non-standard telemetry logic.
- Exception process: Document accepted exceptions with explicit remediation milestones and boundary isolation plans.
</enforcement>
