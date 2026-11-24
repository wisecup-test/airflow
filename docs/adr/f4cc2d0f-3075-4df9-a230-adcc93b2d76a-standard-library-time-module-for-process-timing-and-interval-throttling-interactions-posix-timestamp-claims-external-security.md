---
version: 2
name: f4cc2d0f-3075-4df9-a230-adcc93b2d76a-standard-library-time-module-for-process-timing-and-interval-throttling-interactions-posix-timestamp-claims-external-security
description: "Standard Library time Module for Process Timing and Interval Throttling: Interactions Posix Timestamp Claims External Security"
metadata:
  actual:
    adr_id: f4cc2d0f-3075-4df9-a230-adcc93b2d76a
    status: accepted
    candidate_group_id: 936380ac-53c4-4504-8617-0674ce27de0e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Standard Library time Module for Process Timing and Interval Throttling: Interactions Posix Timestamp Claims External Security

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Core runtime services require reliable elapsed duration calculations for worker health monitoring, cache invalidation, and periodic cleanup tasks.
- Wall-clock temporal functions are subject to external system clock adjustments, leap seconds, and NTP synchronization drift that disrupt loop scheduling.
- Streaming log readers, worker refresh managers, and token security checks require distinct clock guarantees tailored to either monotonic progression or epoch claim validation.

## Problem Statement

Background workers, cache layers, and polling loops require accurate interval measurement and pacing that remain impervious to system clock adjustments, while security token verification requires epoch-based timestamp evaluations. Conflating wall-clock time with process-local duration measurement introduces timing jitter, negative elapsed intervals, and premature timeouts.

## Decision

- **R-LM-001** SHOULD: Interactions with POSIX timestamp claims in external security tokens SHOULD restrict time.time evaluations exclusively to epoch comparison boundaries while delegating persistent timestamps to timezone-aware UTC types.

## Policy Block

In scope:

- In-process timing, rate limiting, and sleep pacing within worker lifecycles, cache revalidation, and streaming log readers.
- Periodic maintenance thresholds, heartbeat calculations, and worker rolling restart intervals.

Out of scope:

- Persistent database timestamp storage and domain entity schema definitions.
- Calendar-based cron and interval timetable expressions requiring timezone-aware datetime calculations.

## Rationale

- The standard library time module provides native primitives for monotonic interval tracking and thread sleeping without introducing external dependencies.
- Employing time.monotonic insulates critical heartbeat, cache revalidation, and worker restart loops from system time adjustments.
- Restricting time.time usage to external epoch validation contracts ensures internal scheduling logic remains isolated from wall-clock discrepancies.

## Consequences

Positive:

- Eliminates timing anomalies and negative duration calculations caused by NTP adjustments or leap seconds in process loops.
- Provides low-overhead, native interval gating for high-frequency cache validation and worker process management.
- Establishes a clear separation between process-local duration mechanics and domain persistence timestamps.

Negative:

- Monotonic clock values cannot be compared across different host machines or persisted across process restarts.
- Developers must maintain vigilance to avoid confusing wall-clock timestamps with process-relative monotonic offsets.

## Alternatives

- Standardize exclusively on datetime.now for both wall-clock persistence and interval calculation (rejected)
  Rejected because: Wall-clock time is vulnerable to system clock adjustments, NTP synchronization jumps, and leap seconds, which can create negative durations or premature timeouts in scheduling loops.
  When valid: Valid only for human-facing calendar scheduling and persistent audit timestamps.
- Adopt a third-party timing and clock abstraction library (rejected)
  Rejected because: Introduces external dependencies for primitive operations that are natively and reliably provided by core runtime libraries.
  When valid: Valid when deterministic time-travel mocking across complex asynchronous distributed topologies is required.

## Risks

- Accidental leakage of time.monotonic values into persistent database columns or cross-host network protocols.
  Mitigation: Enforce strict typing and boundary validation in data models to restrict persistent fields to timezone-aware UTC datetime types.
  Owner: Core engineering team
- Thread blocking during time.sleep calls within cooperative asynchronous execution contexts.
  Mitigation: Ensure synchronous sleep calls are isolated to dedicated worker threads or synchronous loop routines, using asynchronous sleep primitives within event loops.
  Owner: Core engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Distinguish strictly between monotonic progression clocks and wall-clock time: monotonic clocks are invalid across process restarts and must remain local to process memory.
- Configure sleep intervals and refresh thresholds through system configuration parameters rather than hardcoding static constants.

## Continuation Context

Verify commands:

- Discover and execute the project test runner across unit and integration test suites to ensure timing and interval routines pass.
- Discover and execute the project static analysis and linting scripts to verify compliance with standard library import conventions.

Accept when:

- All automated unit and integration tests executing process scheduling, cache revalidation, and stream polling pass successfully.
- Static analysis validates that in-memory elapsed interval calculations rely on monotonic clock sources without wall-clock drift vulnerabilities.

## Enforcement

- Verified by: Automated continuous integration test suites and static analysis linters.
- Verified by: Peer code review for all pull requests affecting runtime process loops, caching intervals, and security token lifetimes.
- Violation handling: Pull requests utilizing wall-clock time for interval measurements or persisting monotonic values are flagged and blocked during code review.
- Exception process: Submit an architectural review request detailing external protocol constraints that necessitate non-monotonic timing primitives.
