---
version: 2
name: 0ecf9c6d-e233-4212-88fe-4a2e197b7f01-centralized-dimensional-metrics-instrumentation-with-stats-interface-metric-tags-not-contain-unbounded-arbitrary
description: "Centralized Dimensional Metrics Instrumentation with stats Interface: Metric Tags Not Contain Unbounded Arbitrary"
metadata:
  actual:
    adr_id: 0ecf9c6d-e233-4212-88fe-4a2e197b7f01
    status: accepted
    candidate_group_id: 25b5d5ad-9c39-4b99-bfe1-8617e1f91e73
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Centralized Dimensional Metrics Instrumentation with stats Interface: Metric Tags Not Contain Unbounded Arbitrary

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Operational subsystems including job runners, state controllers, dag parsers, and execution entities require continuous monitoring of heartbeats, state transitions, scheduling latencies, and cache behavior.
- Diverse metric collection backends require sanitized, primitive key-value tags and fail-safe emission guarantees to avoid degrading system performance during telemetry outages.
- Telemetry emission occurs across high-frequency execution loops and asynchronous supervision threads where inconsistent metric naming or malformed tags lead to runtime failure or silent metric drops.

## Problem Statement

Operational components across the platform require consistent, non-intrusive telemetry to observe heartbeats, task throughput, queue wait times, and deadline violations without binding core business logic to specific monitoring transport protocols or crashing critical execution paths due to backend serialization errors.

## Decision

- **R-MM-001** SHOULD_NOT: Metric tags SHOULD NOT contain unbounded, arbitrary user payloads or unhashed composite objects that risk cardinality explosion or serialization failure.

## Policy Block

In scope:

- All core scheduling processes, job execution runners, background processors, and domain state models.
- Telemetry emission involving heartbeats, task duration, scheduling delays, failure counters, and cache operations.

Out of scope:

- Low-level utility helpers and data serializes that perform pure transformations without operational lifecycle state.
- External client integrations that handle their own transport-level telemetry independently.

Exceptions:

- **EXC-47-001** Telemetry emission occurs within isolated test environments or mock harness implementations where metrics recording is stubbed.
  Approval: Test harness maintainers
  Documentation: Record the mock telemetry configuration within the testing guidelines and inline suite documentation.

## Rationale

- Adopting a unified metrics interface ensures consistent metric names and tag keys across independent daemon processes and models.
- Sanitizing metric tags via dictionary pruning and primitive type coercion prevents unhashable type errors across heterogeneous collector backends.
- Centralizing metric operations protects mission-critical scheduling and task execution paths from telemetry sink failures.

## Consequences

Positive:

- Uniform operational visibility across schedulers, worker supervisors, job runners, and execution models through a consistent telemetry contract.
- Decoupling of core domain models from underlying metric transport and collection protocols.
- Resilient metric emission that safely handles multi-tenant attributes and prevents runtime exceptions from blocking primary execution paths.

Negative:

- Metric call sites introduce slight CPU overhead across high-frequency execution loops such as task state evaluations and cache queries.
- Tag dictionary pruning and serialization require defensive type coercion to prevent serialization failures on complex payload objects.
- Developers must consistently maintain metric naming conventions and tag key schemas across distributed worker and scheduler components.

## Alternatives

- Direct client instrumentation via vendor-specific telemetry SDKs directly in operational models (rejected)
  Rejected because: Couples core execution components to a specific metric collector implementation, duplicates transport logic across disparate modules, and prevents uniform tag pruning and metric name aliasing.
  When valid: Valid only in single-process standalone scripts where no multi-backend abstraction or centralized lifecycle telemetry is maintained.
- Log-based metrics extraction via structured log scraping rather than in-process metric calls (rejected)
  Rejected because: Increases latency for operational alerts, relies on log pipeline ingestion reliability for heartbeat monitoring, and incurs significant I/O overhead for high-frequency operations such as cache evaluations and task state transitions.
  When valid: Valid when telemetry ingestion cannot be embedded in runtime processes or when external log parsers are required for compliance auditing.

## Risks

- Metric backend connectivity failures could propagate exceptions into high-priority execution loops.
  Mitigation: Enforce defensive exception suppression within the metrics layer so metric sink outages never interrupt task execution or scheduling decisions.
  Owner: Core Platform Engineering
- High tag cardinality from user-defined metadata could degrade metrics database storage and ingestion performance.
  Mitigation: Enforce strict tag whitelisting, prune non-primitive values, and avoid using high-cardinality identifiers as metric names.
  Owner: Observability Engineering

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure that metric tag formatting helpers prune empty and None values before telemetry dispatch.
- When instrumenting callbacks or external payloads, serialize non-primitive tag structures to deterministic string formats to maintain backend portability.
- Enclose timing measurements within contextual managers or explicit duration calculations using monotonic or UTC timestamps.

## Continuation Context

Verify commands:

- Discover the repository's test runner and execute the metrics test suite to verify counter and timing emissions.
- Discover the static analysis and linting configuration to verify that metric call sites adhere to centralized abstraction constraints.
- Run unit validation on metric tag sanitization logic to ensure non-primitive attributes and null tags are handled safely.

Accept when:

- All operational and lifecycle instrumentation calls route exclusively through the centralized metrics interface without importing or invoking downstream metric collector clients directly.
- All metric tag payloads pass through tag pruning and serialization sanitization to prevent unhashable complex types and null tags from reaching telemetry backends.
- Verification test suites validate counter increments, timers, and tag schema conformance without raising schema or protocol serialization exceptions.

## Enforcement

- Verified by: Automated static analysis rules verifying that operational components route telemetry exclusively through the centralized metrics abstraction.
- Verified by: Unit and integration test suites validating metric emission counts, timer execution, and tag sanitization behavior.
- Verified by: Peer review checks during code integration ensuring metric naming conventions conform to taxonomy guidelines.
- Violation handling: Pull requests introducing direct metric client invocations or unpruned tag payloads will fail automated compliance checks.
- Violation handling: Flagged violations require refactoring call sites to use standard metrics methods and sanitized tag structures prior to merge approval.
- Exception process: Submit an architecture exception request detailing why the centralized metrics interface cannot fulfill the operational telemetry requirement.
- Exception process: Secure approval from the observability and core platform architecture maintainers before committing non-standard telemetry logic.
- Exception process: Document accepted exceptions with explicit remediation milestones and boundary isolation plans.
