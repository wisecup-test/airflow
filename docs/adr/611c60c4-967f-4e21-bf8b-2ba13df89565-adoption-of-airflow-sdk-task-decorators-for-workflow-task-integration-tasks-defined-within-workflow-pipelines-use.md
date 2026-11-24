---
version: 2
name: 611c60c4-967f-4e21-bf8b-2ba13df89565-adoption-of-airflow-sdk-task-decorators-for-workflow-task-integration-tasks-defined-within-workflow-pipelines-use
description: "Adoption of airflow.sdk Task Decorators for Workflow Task Integration: Tasks Defined Within Workflow Pipelines Use"
metadata:
  actual:
    adr_id: 611c60c4-967f-4e21-bf8b-2ba13df89565
    status: accepted
    candidate_group_id: 3b07933a-4059-4000-8ad1-7153016dbd50
    evidence:
      source: deep
  scope:
    root: kubernetes-tests
    paths:
      - lang_sdk/dags/**
---

# Adoption of airflow.sdk Task Decorators for Workflow Task Integration: Tasks Defined Within Workflow Pipelines Use

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `kubernetes-tests`

Applies to:
- `lang_sdk/dags/**`

## Context

- Workflow pipelines require structured integration boundaries for coordinating discrete data processing and extraction steps across execution stages.
- Pipeline definitions incorporate task decorators provided by airflow.sdk to encapsulate task execution and return boundaries within workflows.
- A standardized convention is necessary to determine how tasks are declared, how task boundaries exchange return values, and how external contracts integrate.

## Problem Statement

Without a unified task declaration standard, pipeline implementations risk inconsistent task boundary definitions, fragmented state transfer mechanisms, and ad-hoc task execution wrappers that complicate workflow maintainability.

## Decision

- **R-IP-001** MUST: Tasks defined within workflow pipelines MUST use the @task decorator from airflow.sdk to declare task boundaries and execution units.

## Policy Block

In scope:

- Workflow pipeline definition modules that declare task execution units and data passing boundaries.

Out of scope:

- Standalone utility modules, non-workflow data processing scripts, and external services invoked outside workflow orchestration boundaries.

## Rationale

- Adopting the @task decorator from airflow.sdk standardizes task boundaries into functional units, simplifying task declaration and interface contracts.
- Functional task decorators allow direct parameter and return-value passing across integration boundaries, reducing boilerplate task wiring.
- The pattern isolates execution units and interfaces with extraction and transformation contracts across workflow boundaries.

## Consequences

Positive:

- Provides a consistent, readable declaration model for workflow tasks using airflow.sdk decorators.
- Encapsulates task execution units and explicit return value interfaces across integration boundaries.

Negative:

- Couples task definition structure to the decorator semantics and runtime contract of airflow.sdk.
- Single-file observation indicates narrow adoption evidence, increasing risk of treating test-specific conventions as global architecture.

## Alternatives

- Legacy operator class definitions without functional task decorators (rejected)
  Rejected because: Classic operator class instantiation requires verbose task wiring and manual message passing configuration compared to functional task decorators.
  When valid: When integrating legacy custom operator classes that lack functional decorator equivalents.
- Adopting airflow.sdk @task decorators for task boundary definitions (accepted)
  When valid: When defining workflow task execution units and message boundaries in Python workflow pipeline definitions.

## Risks

- Incompatible runtime behavior if airflow.sdk decorator API changes across dependency versions.
  Mitigation: Verify resolved dependency versions against repository lock artifacts prior to invoking decorator interfaces.
  Owner: engineering team
- Passing un-serializable objects across decorated task return boundaries causing runtime message queue serialization failures.
  Mitigation: Enforce contract boundaries to return only serializable data payloads between decorated task invocations.
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
- Verify that all decorated task functions export cleanly defined return values matching downstream task signature expectations.
- Ensure task dependencies are defined by passing returned task values or task execution handles according to the orchestration framework contract.

## Continuation Context

Verify commands:

- Discover and execute the project verification script for workflow pipeline integrity and task definition syntax.
- Execute the repository test suite for pipeline integration contracts to confirm decorated tasks execute without serialization errors.

Accept when:

- All workflow tasks declared with the task decorator pass pipeline validation tests without syntax or dependency errors.
- Inter-task data exchanges across decorated task boundaries execute and serialize successfully in the pipeline test suite.

## Enforcement

- Verified by: Automated continuous integration pipeline testing and static analysis linters.
- Verified by: Peer code review for all modifications to workflow pipeline task definitions.
- Violation handling: Pull requests introducing undecorated task functions or invalid task boundary contracts are blocked until corrected.
- Exception process: Exceptions require documented technical justification and approval from the pipeline architecture team.
