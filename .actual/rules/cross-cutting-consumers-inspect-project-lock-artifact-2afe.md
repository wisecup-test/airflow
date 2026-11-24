---
version: 2
name: cross-cutting-consumers-inspect-project-lock-artifact-2afe
description: "OpenTelemetry Distributed Tracing with Context Carrier Propagation: Consumers Inspect Project Lock Artifact Resolve"
metadata:
  actual:
    adr_id: 2afe4de4-1763-4e93-89e0-c236542deb06
    status: accepted
    candidate_group_id: b537e374-721b-4f40-8382-cbf7504e06a8
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# OpenTelemetry Distributed Tracing with Context Carrier Propagation: Consumers Inspect Project Lock Artifact Resolve

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All execution models, routing layers, asynchronous background runners, and worker components managing task lifecycles.

### Rules

- **R-TP-001** MUST: Consumers MUST inspect the project lock artifact to resolve the exact locked version of the tracing library before implementing or modifying instrumentation code.

### Verify

- Discover and execute the project test runner targeting tracing and context propagation test suites.
- Run the repository static analysis and linting checks to verify module-scoped tracer usage across execution models.

**Accept when:**

- Tracing test suites pass, verifying that context carrier dictionaries propagate across run and task lifecycle boundaries.
- Automated contract checks confirm all distributed execution models persist context carriers and extract trace contexts using the standard text map propagator.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis verifying module-scoped tracer instantiation and context carrier persistence.
- Verified by: Integration test suites asserting parent-child span linkage across distributed execution interfaces.
- Violation handling: Continuous integration pipeline blocks code introducing asynchronous execution boundaries without trace context propagation.
- Violation handling: Pull requests lacking tracing context serialization on new execution models are flagged for remediation.
- Exception process: Submit an architectural exception request detailing the execution constraints preventing context carrier persistence.
- Exception process: Require approval from observability platform leads before deploying uninstrumented execution boundaries.
</enforcement>
