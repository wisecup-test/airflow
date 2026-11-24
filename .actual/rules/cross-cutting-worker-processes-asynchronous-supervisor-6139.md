---
version: 2
name: cross-cutting-worker-processes-asynchronous-supervisor-6139
description: "OpenTelemetry Distributed Tracing with Context Carrier Propagation: Worker Processes Asynchronous Supervisors Extract Trace"
metadata:
  actual:
    adr_id: 61395e92-a4ca-48b5-bf6a-5645b6c17d98
    status: accepted
    candidate_group_id: b537e374-721b-4f40-8382-cbf7504e06a8
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# OpenTelemetry Distributed Tracing with Context Carrier Propagation: Worker Processes Asynchronous Supervisors Extract Trace

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All execution models, routing layers, asynchronous background runners, and worker components managing task lifecycles.

### Rules

- **R-TP-001** MUST: Worker processes and asynchronous supervisors MUST extract trace context from the persisted context carrier using the standard text map propagator before initializing execution spans.

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
