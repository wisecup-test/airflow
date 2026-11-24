---
version: 2
name: 0ee55a93-1dff-4db2-8117-8ae6f013c043-canonical-workflow-lifecycle-state-management-via-airflow-utils-state-evaluators-checking-completion-utilize-composite-state
description: "Canonical Workflow Lifecycle State Management via airflow.utils.state: Evaluators Checking Completion Utilize Composite State"
metadata:
  actual:
    adr_id: 0ee55a93-1dff-4db2-8117-8ae6f013c043
    status: accepted
    candidate_group_id: 5cf8ee29-1603-4e40-8d6d-4f9588a37604
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Canonical Workflow Lifecycle State Management via airflow.utils.state: Evaluators Checking Completion Utilize Composite State

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Workflow execution engines require consistent lifecycle state definitions across both internal scheduling logic and external presentation layers.
- Task dependency evaluation rules inspect upstream and current task instances to determine whether execution can proceed.
- FastAPI schemas and UI data models expose task instance and workflow run states to external consumers.
- Using unstandardized string literals or disparate local constants across these subsystems causes synchronization bugs and invalid state transitions.

## Problem Statement

Without a centralized lifecycle state enumeration, core dependency evaluators, schedulers, and API models risk using fragmented string literals and mismatched state definitions, resulting in runtime evaluation bugs and inconsistent API contracts.

## Decision

- **R-LM-001** SHOULD: Evaluators checking for completion MUST utilize composite state collections such as State.finished rather than evaluating individual terminal state values separately.

## Policy Block

In scope:

- Task instance dependency evaluation and scheduling logic.
- API data models, serialization schemas, and status endpoints that report or ingest execution states.
- Internal state transition routines and task status management.

Out of scope:

- External third-party integration points where external protocols require arbitrary external status strings.
- Transient operational statuses not representing task instance or workflow run execution lifecycle states.

## Rationale

- Centralizing workflow states within airflow.utils.state establishes a single source of truth for execution states across both scheduling logic and API schemas.
- Using typed enumerations prevents state mismatches, typo errors, and inconsistent state transition definitions across distributed modules.
- Pre-aggregated state groupings simplify dependency checking logic by allowing set membership queries rather than multiple conditional branches.

## Consequences

Positive:

- Guarantees state consistency between core scheduling mechanisms and user-facing API representations.
- Enables compile-time and static analysis validation of state transitions and model attributes.
- Simplifies maintenance when introducing or modifying lifecycle states across the application.

Negative:

- Couples public and UI API schemas directly to internal workflow engine state representations.
- Requires coordination across both execution and presentation layers when lifecycle states change.

## Alternatives

- Dispersed raw string constants within individual modules (rejected)
  Rejected because: Leads to state string fragmentation, typos, and divergent definitions of terminal states across subsystems.
  When valid: Only in prototype or single-module architectures with minimal state complexity.
- Separate decoupled state enumerations for execution engine and API presentation layers (rejected)
  Rejected because: Introduces boilerplate mapping layers and risks silent synchronization drift between internal execution states and serialized API models.
  When valid: When API contracts are governed by external backwards-compatibility specifications distinct from internal execution engines.

## Risks

- Modifications to internal execution state enumerations may inadvertently introduce breaking changes to downstream API consumers.
  Mitigation: Validate state serialization schemas in API regression suites and use compatibility mapping where necessary.
  Owner: Core Framework Engineering Team
- Performance overhead if state membership checks or conversion routines are executed excessively within hot execution loops.
  Mitigation: Employ optimized sets and enum identity comparisons rather than repetitive dynamic conversions.
  Owner: Core Framework Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- When referencing execution states in dependency checks, utilize composite state sets to evaluate groups of terminal or active states.
- Ensure API schema definitions import state enumerations directly from the canonical state module to preserve end-to-end schema integrity.

## Continuation Context

Verify commands:

- Discover and run the project static analysis suite to verify that all task and DAG run state references import from the canonical state module.
- Discover and execute the project test suite covering task dependency evaluation and API schema validation to confirm state handling compliance.

Accept when:

- Static analysis validates zero occurrences of unmapped raw state strings in dependency checks and API models.
- All unit and integration tests for task dependency evaluation and state reporting pass successfully.

## Enforcement

- Verified by: Automated static typing checks during pull request evaluation.
- Verified by: Mandatory code reviews verifying that state handling imports exclusively from the canonical state module.
- Violation handling: Pull requests introducing raw string literals for execution states or bypassing canonical state models will be blocked.
- Exception process: Exceptions require formal architectural review and approval from the core framework maintainers.
