---
version: 2
name: 476d0ada-e5e1-4922-9de8-d594bf8e2c01-airflow-sdk-task-decorator-integration-pattern-decorated-tasks-requiring-specialized-runtime-routing
description: "Airflow SDK @task Decorator Integration Pattern: Decorated Tasks Requiring Specialized Runtime Routing"
metadata:
  actual:
    adr_id: 476d0ada-e5e1-4922-9de8-d594bf8e2c01
    status: accepted
    candidate_group_id: 92efd180-6006-4339-b017-3a6470acbc6d
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Airflow SDK @task Decorator Integration Pattern: Decorated Tasks Requiring Specialized Runtime Routing

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Integration pipelines require modular units of work that can be scheduled, retried, and distributed across heterogeneous worker queues.
- Legacy workflow architectures relied on verbose custom operator classes that obscured data lineage and complicated inter-task communication.
- Analysis across thirty-two workflow integration files reveals consistent adoption of functional task decorators from airflow.sdk to define queue-bound pipeline tasks.

## Problem Statement

Defining integration workflow tasks through procedural operator inheritance introduces excessive boilerplate, couples business logic to framework infrastructure, and complicates data passing across distributed executor queues. A standardized, functional decorator pattern is required to streamline task declaration, routing, and parameter passing.

## Decision

- **R-IP-001** SHOULD: Decorated tasks requiring specialized runtime routing SHOULD explicitly declare runtime queue and executor configurations within decorator keyword arguments.

## Policy Block

In scope:

- Workflow definitions and data integration pipelines authoring discrete execution tasks.
- Components integrating data transformation steps dispatched across queue and executor boundaries.

Out of scope:

- Infrastructure provisioning logic that operates outside workflow orchestration runtimes.
- Legacy third-party integration operators requiring specialized protocol drivers not representable as standard callable functions.

## Rationale

- The airflow.sdk @task decorator pattern significantly reduces pipeline definition complexity while retaining explicit control over runtime routing parameters such as queues and executor configurations.
- Evidence from thirty-two modules demonstrates consistent usage for dynamic parameter handling, external storage integration, and task-to-task data transfers.
- Functional task declarations improve testability by allowing task logic to be executed and verified as standard callable functions.

## Consequences

Positive:

- Provides concise, functional task definitions that eliminate boilerplate operator subclassing across integration pipelines.
- Establishes clear data lineage and dependency management through explicit function arguments and return values.
- Enables granular declarative attachment of runtime queue designations, retry policies, and executor configurations.

Negative:

- Data serialization overhead can degrade performance if large payloads are passed directly through task return values rather than reference identifiers.
- Requires runtime worker environments across all execution queues to maintain strictly synchronized library versions and serialization contracts.

## Alternatives

- Classic operator class inheritance (rejected)
  Rejected because: Introduces excessive boilerplate, obscures data dependency flow, and increases maintenance friction for standard functional transformations.
  When valid: When interfacing with legacy third-party systems that provide vendor-maintained operator classes with complex multi-stage hook lifecycles.
- Monolithic script execution within single generic tasks (rejected)
  Rejected because: Bypasses granular queue-level task scheduling, fine-grained retry policies, and distributed execution observability across worker pools.
  When valid: When invoking opaque legacy external binaries that cannot be factored into discrete callable functions.

## Risks

- Data serialization overhead when returning large objects from decorated tasks across worker boundaries.
  Mitigation: Persist bulk payloads in external object storage and pass typed reference paths between task boundaries.
  Owner: engineering team
- Dependency version drift between task scheduling nodes and queue worker execution environments.
  Mitigation: Enforce automated container image synchronization and verify locked dependency versions across worker nodes in continuous delivery pipelines.
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
- Separate heavy payload data persistence from task control flow by returning external object storage references rather than raw data in task outputs.
- Ensure task functions remain idempotent and free of unmanaged side effects to support queue dispatch and automatic retries.

## Continuation Context

Verify commands:

- Execute the repository test runner discovered from the dependency manifest to run integration and unit test suites verifying task decorator execution.
- Run the repository static analysis and linting tools discovered from configuration manifests to validate decorator signatures and type annotations.

Accept when:

- Task decorator contracts and functional tasks execute without runtime parameter resolution failures during automated test runs.
- Static analysis validates that all task decorator configurations and return type annotations conform to project rules.

## Enforcement

- Verified by: Continuous integration pipelines running automated unit and integration suites on workflow definitions.
- Verified by: Static analysis and linters validating task decorator signatures and parameter types.
- Verified by: Architecture-focused peer code review for all workflow and integration pipeline changes.
- Violation handling: Automated builds fail when task definitions produce non-serializable return objects across boundaries.
- Violation handling: Code reviews block pull requests introducing unnecessary operator subclassing where decorated functions suffice.
- Exception process: Submit an exception request detailing why procedural operator subclassing or bespoke execution wrappers are required over functional decorators.
- Exception process: Obtain sign-off from integration architecture maintainers before merging non-standard task implementations.
