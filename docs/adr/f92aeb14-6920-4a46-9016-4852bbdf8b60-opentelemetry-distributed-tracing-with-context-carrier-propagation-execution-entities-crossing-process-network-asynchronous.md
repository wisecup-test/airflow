---
version: 2
name: f92aeb14-6920-4a46-9016-4852bbdf8b60-opentelemetry-distributed-tracing-with-context-carrier-propagation-execution-entities-crossing-process-network-asynchronous
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

Date: 2025-02-16
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Distributed workflow orchestration splits execution across decoupled schedulers, execution APIs, asynchronous triggerers, and worker runtimes.
- Standard thread-local or in-process trace context does not survive persistence into metadata stores or asynchronous hand-offs across process boundaries.
- Consistent telemetry requires propagating distributed trace identifiers across state transitions and remote communication channels.

## Problem Statement

Asynchronous workflow execution spans multiple processes, API boundaries, and database state transitions, breaking standard distributed trace continuity unless trace context is systematically serialized, persisted, and propagated across boundaries.

## Decision

- **R-TP-001** MUST: Execution entities crossing process, network, or asynchronous boundaries MUST serialize distributed trace context into a persistent context carrier dictionary.

## Policy Block

In scope:

- All execution models, routing layers, asynchronous background runners, and worker components managing task lifecycles.

Out of scope:

- Local in-memory evaluation utilities, pure functional transformers, and non-distributed CLI helpers that do not cross service or process boundaries.

## Rationale

- Adopting OpenTelemetry provides an industry-standard, vendor-neutral telemetry contract for trace context propagation and span creation across heterogeneous components.
- Serializing trace context into dedicated context carrier attributes on relational execution models bridges the continuity gap inherent in asynchronous, database-mediated task dispatching.
- Module-level tracer resolution standardizes span naming and telemetry configuration throughout the distributed architecture.

## Consequences

Positive:

- End-to-end distributed visibility across asynchronous job runners, scheduling loops, execution APIs, and worker processes.
- Trace context survives database persistence and deferred execution states without losing causal lineage.
- Vendor-neutral observability standard allows seamless integration with standards-compliant telemetry collectors.

Negative:

- Database schema footprint increases slightly to persist serialized context carrier dictionaries on execution models.
- Context propagation requires consistent serialization and deserialization across every distributed communication and persistence boundary.
- Potential trace payload overhead on high-frequency state updates if sampling decisions are unmanaged.

## Alternatives

- In-memory thread-local context propagation without persistence (rejected)
  Rejected because: Fails to span asynchronous boundaries, scheduler deferrals, and decoupled worker executions across independent processes.
  When valid: Valid only in single-process, purely synchronous execution environments.
- Custom proprietary distributed tracing header protocol (rejected)
  Rejected because: Lacks broad ecosystem interoperability, vendor-neutral tooling support, and standardized OpenTelemetry collector ingestion.
  When valid: Valid only when legacy systems forbid external open standards.

## Risks

- High-cardinality span emission causing storage or network saturation in telemetry pipelines.
  Mitigation: Implement head-sampling checks and carrier validation before emitting spans to avoid unnecessary tracing overhead.
  Owner: observability engineering team
- Schema divergence or corrupted context carrier JSON crashing task state transitions.
  Mitigation: Enforce defensive extraction where malformed carriers default to starting a fresh root span without raising runtime exceptions.
  Owner: core platform engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Obtain tracers using module-scoped naming to preserve component namespace hierarchy in emitted spans.
- Ensure context carrier dictionaries are updated whenever parent execution units spawn child or mapped execution instances.
- Explicitly manage span lifecycle start and end timestamps when recording durations across asynchronous job states.

## Continuation Context

Verify commands:

- Discover and execute the project test runner targeting tracing and context propagation test suites.
- Run the repository static analysis and linting checks to verify module-scoped tracer usage across execution models.

Accept when:

- Tracing test suites pass, verifying that context carrier dictionaries propagate across run and task lifecycle boundaries.
- Automated contract checks confirm all distributed execution models persist context carriers and extract trace contexts using the standard text map propagator.

## Enforcement

- Verified by: Automated static analysis verifying module-scoped tracer instantiation and context carrier persistence.
- Verified by: Integration test suites asserting parent-child span linkage across distributed execution interfaces.
- Violation handling: Continuous integration pipeline blocks code introducing asynchronous execution boundaries without trace context propagation.
- Violation handling: Pull requests lacking tracing context serialization on new execution models are flagged for remediation.
- Exception process: Submit an architectural exception request detailing the execution constraints preventing context carrier persistence.
- Exception process: Require approval from observability platform leads before deploying uninstrumented execution boundaries.
