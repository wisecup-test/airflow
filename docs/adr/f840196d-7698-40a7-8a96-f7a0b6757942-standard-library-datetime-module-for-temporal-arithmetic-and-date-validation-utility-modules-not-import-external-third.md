---
version: 2
name: f840196d-7698-40a7-8a96-f7a0b6757942-standard-library-datetime-module-for-temporal-arithmetic-and-date-validation-utility-modules-not-import-external-third
description: "Standard Library Datetime Module for Temporal Arithmetic and Date Validation: Utility Modules Not Import External Third"
metadata:
  actual:
    adr_id: f840196d-7698-40a7-8a96-f7a0b6757942
    status: accepted
    candidate_group_id: 9efdc7f4-3af4-48dd-bae0-bb5409be6b73
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
      - breeze/src/airflow_breeze/**
      - registry/**
---

# Standard Library Datetime Module for Temporal Arithmetic and Date Validation: Utility Modules Not Import External Third

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `airflow_perf/dags/**`
- `breeze/src/airflow_breeze/**`
- `registry/**`

## Context

- Multiple utility scripts, benchmarking workflows, and metadata extractors require temporal calculations, execution scheduling deltas, and date parameter parsing
- Adopting the core datetime module ensures uniform time representations across auxiliary and developer workflows without introducing third-party runtime package dependencies
- Temporal data validation enforces standardized formats for release dates and execution intervals across disparate modules

## Problem Statement

Disparate utility scripts, benchmark suites, and metadata tools require consistent date validation and duration calculations without incurring unnecessary third-party dependency bloat or supply-chain overhead in developer and auxiliary environments.

## Decision

- **R-LM-001** SHOULD_NOT: Utility modules SHOULD NOT import external third-party date parsing dependencies when native datetime functionality satisfies operational requirements.

## Policy Block

In scope:

- Components handling date validation, duration parsing, scheduling deltas, and timestamp extraction within developer tooling and utility modules

Out of scope:

- Core orchestration engine components that require external temporal libraries for complex timezone localization or calendaring rules

Exceptions:

- **EXC-001** An external dependency requires specialized timezone or calendar functionality not provided by native datetime
  Approval: Architecture Review Board
  Documentation: Document exception in the consuming module architecture documentation

## Rationale

- The datetime module provides standardized, dependency-free temporal representation and interval arithmetic across developer tooling and metadata scripts
- Using native datetime and timedelta constructs reduces dependency overhead and eliminates external package maintenance for auxiliary workflows
- Consistent date validation protocols ensure uniform handling of release dates and benchmark execution parameters across disparate modules

## Consequences

Positive:

- Eliminates third-party dependency requirements for developer tooling and benchmarking utilities
- Standardizes duration parsing and delta calculations through uniform timedelta interfaces
- Ensures consistent validation and error handling for temporal parameters across modules

Negative:

- Standard library datetime lacks advanced timezone transition models and business calendar arithmetic out of the box
- Consuming modules must maintain custom parsing expressions for compound duration strings

## Alternatives

- Adopting third-party temporal parsing libraries across all utility scripts (rejected)
  Rejected because: Introduces unnecessary external dependencies and maintenance overhead for standard date validation and interval arithmetic
  When valid: When complex timezone arithmetic, calendar math, or multi-format fuzzy parsing is required across all components
- Using raw Unix timestamps or floating-point epoch seconds for all interval and date calculations (rejected)
  Rejected because: Lacks semantic type safety and readability, increasing the likelihood of unit conversion errors across modules
  When valid: When high-throughput low-level serialization demands raw numerical representations

## Risks

- Timezone-naive datetime objects may lead to subtle calculation discrepancies across different runtime host environments
  Mitigation: Enforce explicit timezone-aware datetime usage or normalized UTC handling in temporal contracts
  Owner: Engineering team
- Custom duration parsing expressions may diverge across different utility modules
  Mitigation: Centralize duration parsing into shared utility functions that return standard timedelta objects
  Owner: Engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Standardize duration parsing by compiling regular expressions that extract days, hours, minutes, and seconds into parameters for timedelta construction
- Ensure date validation functions raise structured parameter exceptions with clear format guidance when validation fails

## Continuation Context

Verify commands:

- # Discover test runner configuration and execute test suites covering date validation
- # Discover and run linting scripts to verify compliance with datetime import constraints

Accept when:

- All date validation and duration calculation test suites execute and pass without errors
- Modules handling dates and time intervals conform to datetime and timedelta interfaces

## Enforcement

- Verified by: Automated test suites executed during continuous integration
- Verified by: Peer code review during pull request evaluation
- Violation handling: Pull requests containing unapproved third-party temporal dependencies or invalid date parsing logic must be blocked until remediated
- Exception process: Submit an architectural review request detailing why standard library datetime constructs are insufficient for the specific use case
