---
version: 2
name: d566583c-8b8c-44ae-ae68-317012ef5f5d-adoption-of-airflow-sdk-for-workflow-task-orchestration-workflow-implementations-isolate-task-orchestration-declarations
description: "Adoption of airflow.sdk for Workflow Task Orchestration: Workflow Implementations Isolate Task Orchestration Declarations"
metadata:
  actual:
    adr_id: d566583c-8b8c-44ae-ae68-317012ef5f5d
    status: accepted
    candidate_group_id: b96b8b42-63af-4816-aa21-0b9027376d89
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
---

# Adoption of airflow.sdk for Workflow Task Orchestration: Workflow Implementations Isolate Task Orchestration Declarations

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `airflow_perf/dags/**`

## Context

- The codebase maintains workflow definitions that coordinate scheduled tasks and dependency graphs across workload pipelines.
- The airflow.sdk module provides a standardized programmatic interface for declaring tasks, establishing upstream dependencies, and accessing execution context.
- Multiple workflow definitions across the workload suite exhibit shared adoption of airflow.sdk alongside task operator abstractions.

## Problem Statement

Without a standardized SDK module for defining workflows, task definitions risk directly coupling with internal scheduler mechanics, resulting in inconsistent task context access, fragmented dependency declaration patterns, and increased maintenance overhead across pipeline definitions.

## Decision

- **R-LM-001** SHOULD: Workflow implementations SHOULD isolate task orchestration declarations from direct execution engine runtime internals.

## Policy Block

In scope:

- Workflow and directed acyclic graph definitions declared in the repository.
- Workload pipeline modules requiring task orchestration and dependency chaining.

Out of scope:

- Standalone data processing scripts that do not execute within the workflow scheduler.
- Utility modules that provide purely arithmetic or string manipulation logic without scheduling context.

Exceptions:

- **EXC-20-001** A legacy workflow requires operators not yet adapted to the standard SDK module interface.
  Approval: Workflow Architecture Reviewers
  Documentation: Record the incompatibility and transition plan in the workflow review ticket.

## Rationale

- Standardizing on airflow.sdk establishes a consistent contract for task authoring and context retrieval across all workflow definitions.
- Explicit dependency management protocols ensure unambiguous upstream and downstream task relationship definitions.
- Decoupling workflow definitions from execution engine internals improves pipeline maintainability and testability across environments.

## Consequences

Positive:

- Provides a unified interface for declaring workflows, task execution parameters, and dependency graphs.
- Reduces architectural coupling between pipeline definitions and underlying scheduler engine internals.
- Standardizes task dependency declaration protocols across disparate workflow modules.

Negative:

- Requires existing workflow definitions to conform to the module API conventions.
- Introduces an architectural dependency on the availability and compatibility of the standard SDK module across all deployment environments.

## Alternatives

- Direct coupling to scheduler core runtime internals without an intermediate SDK layer (rejected)
  Rejected because: Direct coupling to core execution engine classes creates fragility when scheduler internals change and complicates workflow testing outside the full scheduler runtime.
  When valid: Valid only in low-level platform extension modules where direct scheduler engine access is structurally required.
- Adoption of airflow.sdk for workflow definitions and task orchestration (accepted)
  Rejected because: Chosen as the standard programmatic module interface for defining workflow DAGs and task dependencies.
  When valid: Valid across all repository workflow and pipeline definitions requiring task scheduling and dependency chaining.

## Risks

- API surface divergence between the standard SDK module and specialized legacy operators
  Mitigation: Validate operator compatibility against the exact locked library version before migrating legacy workflows.
  Owner: Workflow Engineering Team
- Misalignment between local development dependencies and locked repository environment dependencies
  Mitigation: Enforce strict dependency verification against the repository lock artifact during automated validation phases.
  Owner: Platform Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Locate the project dependency resolution artifact to verify that airflow.sdk is present in the resolved dependency set.
- When declaring task dependencies, maintain explicit chaining through designated upstream methods to ensure deterministic DAG graph construction.

## Continuation Context

Verify commands:

- Discover and run the project test runner to execute workflow validation suites verifying DAG structure and task dependency graphs.
- Discover and run the repository static analysis linter to confirm adherence to approved module import paths and SDK conventions.

Accept when:

- All workflow definitions pass DAG structure checks and dependency resolution validation without errors.
- Task dependency graphs resolve deterministically with valid upstream relationships declared via the SDK interface.
- Static verification checks confirm that workflow modules do not reference unapproved internal engine runtime structures.

## Enforcement

- Verified by: Automated continuous integration pipeline executing static verification and workflow parsing checks.
- Verified by: Peer review on pull requests modifying workflow definition files.
- Violation handling: Pull requests introducing direct imports of deprecated or internal engine modules without SDK usage will be blocked.
- Violation handling: Workflow definitions failing DAG parsing or dependency resolution checks will fail the automated build.
- Exception process: Submit an architectural review request with justification when a legacy component cannot use the standard SDK module.
- Exception process: Document any approved temporary exemption in the central architectural registry.
