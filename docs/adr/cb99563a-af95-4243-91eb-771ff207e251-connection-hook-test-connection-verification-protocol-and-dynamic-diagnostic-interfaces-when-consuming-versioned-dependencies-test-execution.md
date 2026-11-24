---
version: 2
name: cb99563a-af95-4243-91eb-771ff207e251-connection-hook-test-connection-verification-protocol-and-dynamic-diagnostic-interfaces-when-consuming-versioned-dependencies-test-execution
description: "Connection Hook test_connection Verification Protocol and Dynamic Diagnostic Interfaces: When Consuming Versioned Dependencies Test Execution"
metadata:
  actual:
    adr_id: cb99563a-af95-4243-91eb-771ff207e251
    status: accepted
    candidate_group_id: 9440aa7d-0ed7-4846-8c56-526b8610bc1e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Connection Hook test_connection Verification Protocol and Dynamic Diagnostic Interfaces: When Consuming Versioned Dependencies Test Execution

Date: 2025-02-17
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Static analysis identified multiple function definitions bearing test prefixes across API endpoints, data models, and example workflow definitions.
- The Connection data model and corresponding API routes implement dynamic diagnostic methods to probe external service credentials and endpoints at runtime via hook delegation.
- Example workflow definitions define pipeline tasks that verify runtime environment capabilities such as filesystem mount visibility.
- The co-occurrence of test prefixes across runtime diagnostic handlers and workflow definitions represents a lexical heuristic match rather than the unified adoption of a testing framework.

## Problem Statement

The codebase contains function definitions with test prefixes that serve divergent architectural roles, including runtime external service connectivity checks and example workflow tasks. Without explicit separation, runtime diagnostic protocols risk being conflated with development test frameworks, leading to inappropriate dependencies between production service endpoints and automated testing harnesses.

## Decision

- **R-TS-001** MUST: When consuming versioned dependencies for test execution or verification protocols, the consumer MUST discover the ecosystem lock artifact and verify the exact resolved version prior to implementation.

## Policy Block

In scope:

- Connection data models and external hook integrations supporting live connection testing.
- Core API endpoints exposing synchronous or asynchronous external credential validation.
- Workflow task definitions performing live runtime assertions in example workflows.

Out of scope:

- Automated unit, functional, and regression test suites executed during continuous integration.
- Static linting, type validation, and build verification tooling.

## Rationale

- Runtime diagnostic validation requires probing external network endpoints and credentials dynamically, which directly conflicts with the hermetic, reproducible constraints of automated unit testing frameworks.
- Delegating connection verification to specialized hook methods preserves encapsulation and allows individual client integrations to define their own validation routines without leaking protocol details into API handlers.
- The pattern detected across the files reflects operational health verification rather than a testing framework adoption, requiring low confidence and distinct architectural governance.

## Consequences

Positive:

- Isolates operational credential and connectivity health checks from build-time test runner execution.
- Provides a standardized delegation protocol for validating external client connectivity across heterogeneous integration hooks.
- Prevents external service connection attempts from compromising hermetic automated testing environments.

Negative:

- Introduces semantic ambiguity between runtime operational diagnostic routines and offline automated regression test suites.
- Requires individual provider hooks to implement and maintain separate diagnostic execution methods alongside operational client methods.

## Alternatives

- Unify runtime connectivity testing into the automated test framework harness (rejected)
  Rejected because: Automated test framework harnesses are designed for hermetic continuous integration environments and are unsuitable for driving live external network connectivity probes in running service instances.
  When valid: Valid only during offline integration testing in isolated staging infrastructure where mocked external services are configured.
- Direct invocation of unit testing runners within API boundary endpoints (deferred)
  Rejected because: Standard test runner frameworks require separate execution runtimes and cannot be directly queried over synchronous HTTP API endpoints without excessive latency and process overhead.
  When valid: Valid when running full-suite regression validation during pre-deployment checks.

## Risks

- Dynamic external connection testing can cause thread starvation or latency spikes when connecting to unreachable external hosts.
  Mitigation: Execute connection diagnostic probes through asynchronous worker queues or background execution pools rather than blocking synchronous API threads.
  Owner: engineering team
- Heuristic naming collisions may lead developers to implement production runtime logic inside development test directories or vice versa.
  Mitigation: Enforce naming conventions that clearly distinguish between operational runtime verification routines and development test harnesses.
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
- Implement runtime connection verification using defensive delegation that checks for hook diagnostic method support before invocation.
- Ensure API diagnostic endpoints respect administrative enablement configurations and strict permission dependencies prior to executing external probes.

## Continuation Context

Verify commands:

- Discover and execute the project automated test suite via the repository configuration to ensure all tests pass.
- Discover and run static type checking across API and model directories to verify diagnostic method contract compliance.

Accept when:

- Project automated test suites execute cleanly with zero test failures.
- Runtime connectivity test interfaces return structured status indicators without exposing unhandled client exceptions to callers.

## Enforcement

- Verified by: Automated static analysis rules verifying that runtime diagnostic endpoints remain isolated from unit testing suites.
- Verified by: Continuous integration test runners executing standard project test suites.
- Verified by: Peer code review for all new hook implementations and connection route definitions.
- Violation handling: Code introducing test runner dependencies into runtime models or API boundaries must be rejected in pull request review.
- Violation handling: Hook implementations that fail to return structured diagnostic status from validation methods must be refactored before merging.
- Exception process: Exceptions to runtime verification protocols must be reviewed by the architecture review board and documented in the integration design record.
- Exception process: Temporary bypass of diagnostic endpoints requires explicit configuration toggles in the core configuration registry.
