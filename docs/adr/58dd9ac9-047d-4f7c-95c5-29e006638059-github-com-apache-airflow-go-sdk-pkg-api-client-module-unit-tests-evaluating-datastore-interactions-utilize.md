---
version: 2
name: 58dd9ac9-047d-4f7c-95c5-29e006638059-github-com-apache-airflow-go-sdk-pkg-api-client-module-unit-tests-evaluating-datastore-interactions-utilize
description: "github.com/apache/airflow/go-sdk/pkg/api Client Module: Unit Tests Evaluating Datastore Interactions Utilize"
metadata:
  actual:
    adr_id: 58dd9ac9-047d-4f7c-95c5-29e006638059
    status: accepted
    candidate_group_id: 1813deb9-82ef-488c-a06f-59ea8422b5cb
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - edge/**
      - pkg/api/**
      - pkg/config/**
      - pkg/edgeapi/**
      - pkg/logging/server/**
      - sdk/**
---

# github.com/apache/airflow/go-sdk/pkg/api Client Module: Unit Tests Evaluating Datastore Interactions Utilize

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `edge/**`
- `pkg/api/**`
- `pkg/config/**`
- `pkg/edgeapi/**`
- `pkg/logging/server/**`
- `sdk/**`

## Context

- Distributed worker processes and execution runtimes require access to orchestration datastore entities including variables, connections, task instances, and dag runs.
- Direct database connectivity from distributed execution workers to central relational storage introduces connection pool exhaustion, schema coupling, and security boundary erosion.
- The architecture establishes dedicated client interfaces within the internal api module to mediate all read and write interactions with primary datastore entities across network boundaries.
- Mock client implementations companioning each domain client interface provide isolated unit testing contracts without requiring live datastore connections.

## Problem Statement

Distributed workers and task execution processes require access to primary datastore entities such as configuration variables, connection credentials, and task execution state. Exposing direct database connections to distributed execution runtimes causes architectural coupling, presents credential distribution hazards, and scales poorly against database connection limits. The system requires an architectural boundary that isolates primary datastore access behind standardized client interfaces and remote communication protocols.

## Decision

- **R-PD-001** SHOULD: Unit tests evaluating datastore interactions SHOULD utilize corresponding mock client implementations rather than initiating external network connections.

## Policy Block

In scope:

- Code executing within distributed worker runtimes, task execution handlers, and client packages requiring datastore operations.
- Components reading or modifying variables, connections, assets, task instances, or dag runs.

Out of scope:

- Central metadata service implementations that manage and query underlying physical database tables directly.
- Local transient memory structures operating purely within single-process execution boundaries.

Exceptions:

- **EXC-28-001** Bootstrap initialization sequences where remote API services are unreachable and local environment overrides are explicitly configured.
  Approval: Architecture Review Board
  Documentation: Documented in local configuration manifests with architectural justification.

## Rationale

- Decoupling execution workers from direct database connectivity protects primary datastores from connection starvation under heavy concurrency workloads.
- Centralizing datastore access within dedicated client interfaces enforces consistent serialization, authentication, and error propagation semantics.
- Providing explicit interface contracts enables predictable mocking and test isolation across client-consuming packages.

## Consequences

Positive:

- Primary datastore connection exhaustion is prevented across highly concurrent worker clusters.
- Internal schema changes in central datastores are insulated behind stable API contracts without breaking worker processes.
- Test suites can verify datastore interactions reliably using mock client implementations without requiring external datastore instances.

Negative:

- Datastore queries incur network serialization latency compared to direct database queries.
- Engineers must maintain client interface signatures and mock implementations across service revisions.

## Alternatives

- Direct database driver connectivity from worker nodes to central relational storage (rejected)
  Rejected because: Exhausts database connection pools under concurrent task execution and exposes database schemas directly to distributed worker environments
  When valid: Monolithic single-node architectures where execution workers and database instances reside on the same physical host
- Unstructured HTTP client calls using ad-hoc request invocations across each component (rejected)
  Rejected because: Duplicates serialization, request construction, and error handling while preventing structured mocking in unit test suites
  When valid: Ad-hoc diagnostic scripts and one-off administrative utilities

## Risks

- API endpoint unavailability blocks worker execution and variable resolution.
  Mitigation: Implement client-side retries, exponential backoff, and local environment variable fallbacks.
  Owner: Distributed Systems Engineering Team
- Interface drift between client contracts and remote API server schemas.
  Mitigation: Automate client contract generation and enforce contract validation suites in continuous integration.
  Owner: Platform SDK Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Client instances should be constructed with request middleware configured for authentication headers and correlation identifiers.
- Context instances passed into client calls must propagate appropriate timeouts to prevent blocked worker threads during network partitions.

## Continuation Context

Verify commands:

- Discover the project verification script from the repository manifest and execute test suites covering client interface implementations.
- Discover and run static analysis directives to confirm datastore interactions occur exclusively via client interfaces.

Accept when:

- All test suites for client interfaces and mock implementations execute successfully with zero failures.
- No direct database connection routines or raw unauthenticated requests are present within distributed worker packages.

## Enforcement

- Verified by: Automated continuous integration pipeline executing repository test and lint suites.
- Verified by: Mandatory peer review on pull requests touching datastore client packages.
- Violation handling: Pull requests bypassing client interface abstractions are blocked from merging.
- Violation handling: Non-compliant code must be refactored to use standard client interfaces prior to release.
- Exception process: Submit an architectural exception request detailing technical blockers preventing client usage.
- Exception process: Approval required from Platform Architecture Lead and Security Team.
