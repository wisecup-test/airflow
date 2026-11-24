---
version: 2
name: 9a9cfd10-bc3b-4723-971c-d7cac5b51ca2-pendulum-library-adoption-for-timezone-aware-datetime-and-schedule-management-modules-responsible-workflow-scheduling-timetable-evaluation
description: "Pendulum Library Adoption for Timezone-Aware Datetime and Schedule Management: Modules Responsible Workflow Scheduling Timetable Evaluation"
metadata:
  actual:
    adr_id: 9a9cfd10-bc3b-4723-971c-d7cac5b51ca2
    status: accepted
    candidate_group_id: f7deb672-8439-4e12-a79b-6a45f983672e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Pendulum Library Adoption for Timezone-Aware Datetime and Schedule Management: Modules Responsible Workflow Scheduling Timetable Evaluation

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Workflow scheduling and data interval calculations require strict, unambiguous timezone handling across international boundaries and daylight saving time transitions.
- Standard datetime implementations often permit naive datetimes and exhibit inconsistent timezone offset arithmetic, leading to calculation discrepancies in automated scheduling loops.
- Static analysis detects consistent adoption of the Pendulum library across thirty-eight modules spanning core scheduling models, timetable implementations, API logging decorators, and pipeline definitions.
- Timetable components and execution date records require precise serialization and deserialization of ISO-8601 timestamps alongside exact interval boundaries.

## Problem Statement

Distributed workflow orchestration relies on deterministic temporal calculations to trigger tasks, compute data intervals, and track execution metadata. Relying on standard datetime operations introduces risks of naive timestamp contamination, ambiguous daylight saving transitions, and inconsistent cross-platform timezone resolution, resulting in missed schedules or data interval miscalculations.

## Decision

- **R-LM-001** MUST: All modules responsible for workflow scheduling, timetable evaluation, data interval computation, and execution timestamp tracking MUST use the Pendulum library for temporal arithmetic and timezone resolution.

## Policy Block

In scope:

- Core scheduling abstractions, data interval calculations, and timetable plugins
- Workflow definitions and task decorators performing date or time manipulation
- API decorators and logging layers handling request or execution timestamps

Out of scope:

- Third-party libraries with internal, encapsulated datetime representations
- Static assets and documentation not involved in temporal logic

Exceptions:

- **EXC-20-001** Integrating with external protocols or database drivers requiring raw native datetime objects
  Approval: Architecture review board and core maintainers
  Documentation: Explicit inline architecture comment documenting the conversion boundary

## Rationale

- Pendulum guarantees timezone awareness by default, preventing subtle bugs caused by naive timestamp arithmetic during daylight saving time shifts and cross-timezone execution.
- Codebase evidence across thirty-eight files shows consistent usage of Pendulum across domain models, timetable event generators, and user-facing workflow examples.
- Providing standardized duration, period, and exact interval interfaces simplifies timetable logic and eliminates fragmented datetime utilities across orchestration components.

## Consequences

Positive:

- Deterministic data interval and next-run calculations across varied timezones and daylight saving transitions
- Standardized temporal API surface across core models, custom timetables, and workflow scripts
- Prevention of timezone-naive timestamp bugs in persistent scheduler tables and serialized representations

Negative:

- Introduction of an external third-party dependency with potential version coupling across execution environments
- Conversion overhead when interfacing with native datetime objects expected by external libraries or database layers
- Memory and serialization overhead compared to primitive integer or native datetime structures

## Alternatives

- Standard library datetime module with manual timezone management (rejected)
  Rejected because: Lacks default timezone-aware safety, requires manual handling of daylight saving ambiguities, and leads to error-prone data interval calculations.
  When valid: Minimal utility scripts with zero external dependency requirements and no timezone-sensitive scheduling.
- Python dateutil extension library (rejected)
  Rejected because: Does not provide comprehensive duration, period, and immutable timezone-aware datetime constructs equivalent to Pendulum.
  When valid: Lightweight parsing of heterogeneous timestamp strings without complex interval arithmetic.

## Risks

- Version divergence between core orchestrator runtime and user workflow execution environments
  Mitigation: Adhere to the repository lock artifact and validate compatibility across dependency environments
  Owner: Core Orchestration Team
- Performance overhead from excessive timezone object instantiations within high-frequency scheduler loops
  Mitigation: Cache timezone lookups and reuse static UTC instances where dynamic conversions are unnecessary
  Owner: Scheduler Performance Working Group

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure all temporal arithmetic in timetable extensions uses timezone-aware constructors rather than naive instances.
- Convert raw datetime inputs at boundary interfaces into Pendulum instances immediately upon ingestion.
- Verify that serialization mechanisms preserve timezone metadata when storing schedule definitions.

## Continuation Context

Verify commands:

- # Discover repository test runner and execute timetable and temporal calculation test suites
- # Discover static analysis and linting configuration to verify timezone-aware import adherence

Accept when:

- All automated test suites covering scheduling, timetables, and interval calculations pass without errors
- Static analysis confirms no naive datetime instantiations exist in scheduling and timetable paths
- Code review confirms all temporal arithmetic relies on the adopted library

## Enforcement

- Verified by: Automated continuous integration test suites
- Verified by: Static analysis and linter rules checking temporal imports
- Verified by: Mandatory peer code review for scheduling and timetable modules
- Violation handling: Pull requests introducing naive datetime arithmetic or unapproved datetime libraries are blocked from merging
- Violation handling: Violating code must be refactored to use the standardized Pendulum patterns before approval
- Exception process: Submit an architectural exception request detailing interoperability constraints with external systems
- Exception process: Approval required from core maintainers accompanied by explicit conversion boundary documentation
