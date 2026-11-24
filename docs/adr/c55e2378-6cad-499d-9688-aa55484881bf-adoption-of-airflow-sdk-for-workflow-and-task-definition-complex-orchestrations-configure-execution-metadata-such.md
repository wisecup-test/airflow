---
version: 2
name: c55e2378-6cad-499d-9688-aa55484881bf-adoption-of-airflow-sdk-for-workflow-and-task-definition-complex-orchestrations-configure-execution-metadata-such
description: "Adoption of airflow.sdk for Workflow and Task Definition: Complex Orchestrations Configure Execution Metadata Such"
metadata:
  actual:
    adr_id: c55e2378-6cad-499d-9688-aa55484881bf
    status: accepted
    candidate_group_id: 42f92a7f-c357-4f51-84d8-f32a901679a0
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of airflow.sdk for Workflow and Task Definition: Complex Orchestrations Configure Execution Metadata Such

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Multiple workflow definitions and task implementations require a standardized programmatic interface to declare execution units and dependencies.
- Direct dependency on internal orchestrator data models and execution infrastructure creates tight coupling and impedes modular platform evolution.
- Codebase analysis across 44 files indicates widespread adoption of airflow.sdk for task definition, decorator patterns, and pipeline construction.

## Problem Statement

Workflow pipelines require a standardized, decoupled interface for authoring tasks, managing execution dependencies, and passing parameters without binding task implementations directly to orchestrator database models or execution engine internals.

## Decision

- **R-LM-001** MAY: Complex orchestrations MAY configure execution metadata such as retries, timeouts, triggers, and asset references directly on @task decorator definitions.

## Policy Block

In scope:

- Authoring workflow definitions, data pipelines, and scheduled DAGs across repository modules.
- Implementing task execution functions, data transformation steps, and asset-based triggers.

Out of scope:

- Low-level orchestrator infrastructure engines, internal database migration scripts, and core message queue transports.
- External client integrations that interface with the platform exclusively through external HTTP REST APIs.

## Rationale

- Centralizing workflow authoring around airflow.sdk establishes a clear architectural boundary between user pipeline definitions and internal platform infrastructure.
- The pattern is evidenced across 44 files with high significance, showing consistent reliance on SDK decorators and interfaces for task orchestration.
- Using the SDK reduces maintenance friction and simplifies pipeline unit testing by abstracting low-level scheduler and database mechanics.

## Consequences

Positive:

- Provides a consistent, declarative developer experience for task authoring through decorators and standardized data passing.
- Decouples pipeline task definitions from internal database schemas and scheduler models, minimizing breaking changes across platform updates.
- Facilitates isolated testing and validation of task logic without requiring active database connections or heavyweight orchestration runtime state.

Negative:

- Requires migrating existing custom operator classes and legacy task patterns to align with SDK interfaces.
- Introduces an abstraction layer that can mask underlying scheduler communication mechanics during debugging.

## Alternatives

- Direct inheritance from legacy operator base classes and manual database model queries (rejected)
  Rejected because: Couples user-facing workflows directly to orchestrator database schemas and internal execution state, preventing modular runtime evolution.
  When valid: Valid only in legacy subsystems where required operator capabilities have not yet been exposed via the SDK.
- Standalone bespoke script invocations without orchestration SDK integration (rejected)
  Rejected because: Eliminates platform-managed dependency tracking, parameter serialization, context injection, and asset triggering capabilities.
  When valid: Valid for external standalone utility scripts executed entirely outside the orchestration framework.

## Risks

- Discrepancies between resolved SDK versions across different deployment environments could cause subtle task runtime failures.
  Mitigation: Strict enforcement of lock-version verification and automated dependency alignment across development and runtime environments.
  Owner: engineering team
- Task authors may inadvertently import internal orchestrator modules instead of public SDK interfaces.
  Mitigation: Enforce import boundary validation via continuous integration static analysis.
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
- When defining workflows, import task decorators and workflow constructs directly from the designated SDK namespace rather than core internal modules.
- Use native return values within decorated task functions to allow the SDK to manage serialization and data dependencies automatically.

## Continuation Context

Verify commands:

- Discover the repository test runner from the root configuration and execute unit and integration test suites covering workflow definitions.
- Discover the static analysis and linting verification scripts from the repository build configuration and execute them to check module import boundaries.

Accept when:

- All discovered workflow and task test suites execute and pass without import or runtime errors.
- Static analysis checks confirm all task definitions and workflow declarations reference the approved SDK module without unauthorized internal model access.

## Enforcement

- Verified by: Automated continuous integration test and lint pipelines executed against proposed changes.
- Verified by: Peer code review verifying compliance with SDK import standards and task decorator conventions.
- Violation handling: Automated build failure when unapproved internal model imports or deprecated operator patterns are detected.
- Violation handling: Rejection of pull requests introducing direct database queries or non-standard task execution wrappers.
- Exception process: Formal architectural review submission documenting specific technical limitations requiring direct internal model access.
- Exception process: Temporary time-bound waiver approved by platform engineering leadership with scheduled migration milestones.
