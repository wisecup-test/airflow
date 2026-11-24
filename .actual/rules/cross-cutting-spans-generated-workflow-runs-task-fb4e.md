---
version: 2
name: cross-cutting-spans-generated-workflow-runs-task-fb4e
description: "OpenTelemetry Distributed Tracing with Context Carrier Propagation: Spans Generated Workflow Runs Task Attempts"
metadata:
  actual:
    adr_id: fb4e37a0-ccbf-404a-89a1-96298c0af0c5
    status: accepted
    candidate_group_id: b537e374-721b-4f40-8382-cbf7504e06a8
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# OpenTelemetry Distributed Tracing with Context Carrier Propagation: Spans Generated Workflow Runs Task Attempts

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All execution models, routing layers, asynchronous background runners, and worker components managing task lifecycles.

### Rules

- **R-TP-001** SHOULD: Spans generated for workflow runs and task attempts SHOULD explicitly record start and end timestamps matching execution state transitions.

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
