---
version: 2
name: 796c94a0-33eb-4230-9a9a-29d1921854f1-airflow-and-airflow-sdk-library-integration-consumers-inspect-repository-lock-artifact-resolve
description: "Airflow and Airflow SDK Library Integration: Consumers Inspect Repository Lock Artifact Resolve"
metadata:
  actual:
    adr_id: 796c94a0-33eb-4230-9a9a-29d1921854f1
    status: accepted
    candidate_group_id: c8078bf1-61a3-47de-bb54-fad03972f61c
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/docs/**
      - src/tests_common/test_utils/**
---

# Airflow and Airflow SDK Library Integration: Consumers Inspect Repository Lock Artifact Resolve

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/docs/**`
- `src/tests_common/test_utils/**`

## Context

- Shared testing utilities and documentation generators require consistent interfaces against orchestration execution models and plugin management subsystems.
- Orchestration lifecycle components, including interval scheduling, timetable serialization, and plugin discovery, define domain contracts that test fixtures and mock environments must accurately emulate.
- Internal interfaces evolve between core orchestration packages and dedicated runtime software development kits, necessitating explicit compatibility layers in shared support modules.

## Problem Statement

Shared test suites, mock utilities, and documentation generators across the repository require unified access to orchestration lifecycle models, plugin managers, and timetable contracts. Without a disciplined architectural adoption of the primary orchestration library and its companion software development kit interfaces, test harnesses risk drift, mock mismatches across runtime environments, and brittle coupling to undocumented internal structures.

## Decision

- **R-LM-001** MUST: Consumers MUST inspect the repository lock artifact to resolve the exact locked version of the orchestration library before implementing or updating contract implementations.

## Policy Block

In scope:

- Shared testing infrastructure and mock utilities modeling orchestration behavior.
- Documentation generation utilities inspecting provider packages and configuration metadata.
- Performance evaluation kits measuring orchestration memory and process resource utilization.

Out of scope:

- End-user workflow definitions that do not provide shared test fixtures or documentation infrastructure.
- Isolated third-party utility scripts that operate entirely outside orchestration scheduling and plugin contexts.

## Rationale

- Adopting the core orchestration library and SDK contracts ensures test doubles and timetable mocks remain faithful to the runtime execution semantics.
- Centralizing plugin manager mocking across both core and SDK interfaces prevents regression failures across differing runtime execution modes.
- Encapsulating version compatibility logic in dedicated helpers isolates support harnesses from internal contract deprecations.

## Consequences

Positive:

- Guarantees test doubles and mock plugins faithfully match runtime behavior across both legacy core and modern SDK execution paths.
- Prevents divergence between documentation metadata extraction and repository package structure.
- Eliminates duplicate scheduling abstractions across isolated test suites.

Negative:

- Binds shared testing and documentation utilities directly to internal framework interfaces, requiring maintenance when internal APIs change.
- Increases the dependency footprint of test utility suites by requiring the full orchestration framework and SDK.

## Alternatives

- Independent Mock Interfaces (rejected)
  Rejected because: Creating disconnected mock interfaces leads to behavioral drift and fails to detect API signature incompatibilities across framework releases.
  When valid: Valid only in lightweight unit tests where no orchestration semantics or serialization behaviors are exercised.
- Direct Subclassing Without Compatibility Wrappers (rejected)
  Rejected because: Directly binding tests to a single framework namespace breaks compatibility during structural transitions between core modules and decoupled SDK modules.
  When valid: Valid in environments locked permanently to a single unchanging major framework release.

## Risks

- Internal API refactoring in the core orchestration library or SDK can break mock patch targets and test harnesses.
  Mitigation: Encapsulate patch targets within centralized mock manager functions and maintain version compatibility abstraction helpers.
  Owner: engineering team
- Performance overhead in test harnesses importing heavy orchestration framework modules.
  Mitigation: Scope imports of framework modules to test execution boundaries and reuse shared fixtures where appropriate.
  Owner: engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure that custom timetable implementations intended for testing faithfully adhere to serialization protocols and interval calculation invariants specified by the base class.
- When mocking plugin discovery mechanisms, verify both legacy and SDK namespaces to ensure test harnesses function seamlessly across active runtime variants.

## Continuation Context

Verify commands:

- Discover the repository test execution script and run the test suite targeting shared testing utilities.
- Discover the repository documentation build script and verify that provider documentation generation completes without configuration errors.

Accept when:

- All shared test harness suites pass verification against both core and SDK interfaces.
- Documentation generation scripts successfully resolve package metadata and generate documentation artifacts without contract errors.

## Enforcement

- Verified by: Automated continuous integration test suites executing across all supported runtime matrix configurations.
- Verified by: Code review validation for any updates to shared test harnesses, mock managers, or documentation helpers.
- Violation handling: Pull requests introducing unencapsulated version checks or out-of-contract timetable implementations will be rejected by review and blocked by automated continuous integration failures.
- Exception process: Exceptions require approval from the core framework architecture maintainers accompanied by an issue tracking the migration plan.
