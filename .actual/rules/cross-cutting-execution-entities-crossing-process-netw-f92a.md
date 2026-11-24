---
version: 2
name: cross-cutting-execution-entities-crossing-process-netw-f92a
description: "OpenTelemetry Distributed Tracing with Context Carrier Propagation: Execution Entities Crossing Process Network Asynchronous"
metadata:
  actual:
    adr_id: f92aeb14-6920-4a46-9016-4852bbdf8b60
    status: accepted
    candidate_group_id: b537e374-721b-4f40-8382-cbf7504e06a8
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# OpenTelemetry Distributed Tracing with Context Carrier Propagation: Execution Entities Crossing Process Network Asynchronous

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All execution models, routing layers, asynchronous background runners, and worker components managing task lifecycles.

### Rules

- **R-TP-001** MUST: Execution entities crossing process, network, or asynchronous boundaries MUST serialize distributed trace context into a persistent context carrier dictionary.

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
