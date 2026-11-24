---
version: 2
name: 1533615e-08a3-478b-81c1-6dd6bfa3b969-airflow-models-core-domain-modeling-consumers-model-connection-configurations-credentials-airflow
description: "airflow.models Core Domain Modeling: Consumers Model Connection Configurations Credentials Airflow"
metadata:
  actual:
    adr_id: 1533615e-08a3-478b-81c1-6dd6bfa3b969
    status: accepted
    candidate_group_id: 79d5e955-c5e1-4253-94ae-0027687a0c08
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# airflow.models Core Domain Modeling: Consumers Model Connection Configurations Credentials Airflow

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/tests_common/test_utils/**`

## Context

- Shared test utilities and system helpers across the repository require consistent representations of orchestration entities, connections, and execution records.
- Without standardized domain models, disparate testing modules introduce incompatible dictionary representations, inconsistent hook mocks, and fragmented validation logic.
- The codebase standardizes on airflow.models across test helpers, mock hooks, and integration harnesses to represent connections and query execution metadata.

## Problem Statement

Test utilities and system helpers that configure external system connections and inspect execution state risk drifting from runtime data structures if connection schemas and orchestration metadata are represented via ad-hoc dictionaries or unvalidated mock objects.

## Decision

- **R-LM-001** MUST: Consumers MUST model connection configurations and credentials using airflow.models.Connection rather than unvalidated primitive structures.

## Policy Block

In scope:

- Shared test utility packages, system connection helpers, and mock hook implementations interacting with orchestration connections and execution records.
- Integration test fixtures validating task execution and credential provisioning.

Out of scope:

- Unit tests isolated entirely from orchestration models and connection abstractions.
- Third-party external libraries operating independently of the orchestration framework.

Exceptions:

- **EXC-20-001** A specialized mock protocol requires simulated transport failure prior to connection object initialization.
  Approval: Architecture Review Board or Core Maintainers
  Documentation: Document the protocol-level mock requirements in the associated test module header.

## Rationale

- Observed implementation across four shared test modules demonstrates uniform adoption of airflow.models for connection instantiation and execution log queries.
- Standardizing on airflow.models.Connection and AirflowException prevents schema drift between test harness utilities and production runtime behavior.
- Encapsulating connection resolution within mock hook subclasses ensures predictable test invocations and deterministic credential propagation.

## Consequences

Positive:

- Guarantees structural parity between test connection instances and production execution contracts.
- Reduces code duplication across system test helpers by centralizing credential parsing and connection provisioning.
- Provides reliable execution state inspection through canonical model queries.

Negative:

- Couples test utility packages directly to core orchestration model dependencies.
- Requires test harnesses to be updated when core model attributes or constructor signatures evolve.

## Alternatives

- Ad-hoc dictionary payloads for test connection provisioning (rejected)
  Rejected because: Dictionaries lack schema enforcement, allowing credential parameter mismatches and silent test regressions.
  When valid: Lightweight unit tests where external connection interfaces are fully bypassed.
- Standalone mock classes disconnected from core framework models (rejected)
  Rejected because: Disconnected mock classes fail to validate compatibility with core hook contracts and increase maintenance overhead.
  When valid: Testing standalone third-party libraries without framework dependencies.

## Risks

- Breaking changes in core model initialization parameters could impact shared test helpers across multiple modules.
  Mitigation: Enforce strict lockfile resolution and run regression test suites verifying helper initialization contracts.
  Owner: Engineering Team
- Heavyweight dependencies imported into test utilities might increase test runner startup overhead.
  Mitigation: Structure helper imports lazily or scope test utilities strictly to integration and system test contexts.
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- When constructing mock hooks, inherit directly from the target hook class and override invocation counters alongside connection return methods.
- When parsing credentials from external sources, validate input structures and raise framework exceptions when encountering missing or invalid configuration keys.

## Continuation Context

Verify commands:

- Discover and execute the project integration test suite targeting shared test utilities and connection helpers.
- Discover and run the project static type checker to verify model constructor arguments and hook inheritance compatibility.

Accept when:

- All test helper modules instantiate connections using airflow.models.Connection without parameter type errors.
- Integration test suites using MockedHook and connection provisioning helpers pass without schema discrepancies.
- Static type inspection confirms compliance with core model attributes and exception types.

## Enforcement

- Verified by: Automated continuous integration test suites executing integration and system helper tests.
- Verified by: Static type verification and lint analysis in pull request validation pipelines.
- Verified by: Peer code review verifying adherence to canonical model usage.
- Violation handling: Pull requests introducing raw dictionary connection mocks or bypassed entity structures are blocked by automated review gates.
- Violation handling: Violations must be refactored to consume canonical model abstractions before code merge.
- Exception process: Submit an architectural exception request detailing why canonical domain models cannot be utilized in the specific test scenario.
- Exception process: Obtain approval from the architecture review board or project maintainers prior to merging an exception.
