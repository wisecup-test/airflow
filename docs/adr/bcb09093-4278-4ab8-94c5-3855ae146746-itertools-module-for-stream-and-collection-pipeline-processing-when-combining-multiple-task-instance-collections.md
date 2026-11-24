---
version: 2
name: bcb09093-4278-4ab8-94c5-3855ae146746-itertools-module-for-stream-and-collection-pipeline-processing-when-combining-multiple-task-instance-collections
description: "itertools Module for Stream and Collection Pipeline Processing: When Combining Multiple Task Instance Collections"
metadata:
  actual:
    adr_id: bcb09093-4278-4ab8-94c5-3855ae146746
    status: accepted
    candidate_group_id: 796cd48c-7a0e-4ce6-9ad5-1ffcdc0bfc03
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# itertools Module for Stream and Collection Pipeline Processing: When Combining Multiple Task Instance Collections

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Core workflow scheduling, execution state management, and log streaming routines process large volumes of entities and event records.
- Materializing intermediate collections during batch scheduling, stream buffering, or composite database query construction introduces unnecessary memory allocations and garbage collection pressure.
- The codebase requires consistent, lazy iterator composition primitives across scheduling loops, API endpoints, timetable evaluations, and logging accumulators.
- The itertools module provides functional iterator building blocks that execute in native runtime code for high-throughput stream and sequence manipulation.

## Problem Statement

High-throughput orchestration components frequently process large collections of task instances, log streams, and scheduling candidates. Generating intermediate lists or nested collection copies during sequence concatenation, slicing, or combinatorial key filtering degrades memory efficiency and increases latency in hot scheduling and execution paths.

## Decision

- **R-LM-001** MUST: When combining multiple task instance collections or candidate sequences for scheduling evaluation, implementations MUST use itertools.chain rather than list concatenation or in-place list mutation.

## Policy Block

In scope:

- Core scheduling loops, task instance state transitions, and execution decision algorithms.
- Log streaming, buffer accumulation, and event stream processing components.
- Database query batch generation and timetable sequence evaluation routines.

Out of scope:

- Data structures requiring random index access or persistent multi-pass collection mutation.
- Relational database ORM entity definitions and static configuration mapping tables.

Exceptions:

- **EXC-20-001** A downstream consumer API explicitly requires a materialized sequence for serialization or length determination.
  Approval: Core Architecture Review
  Documentation: Document rationale for materialization and verified collection size bounds in the calling function.

## Rationale

- Evidence across fifteen core modules indicates widespread adoption of itertools primitives for stream slicing, candidate concatenation, and Cartesian product generation.
- Lazy evaluation with native iterator building blocks eliminates intermediate collection memory allocations in critical scheduling and streaming hot paths.
- Standardizing on itertools guarantees predictable memory characteristics when processing tens of thousands of workflow tasks and log streams.

## Consequences

Positive:

- Reduced memory consumption and garbage collection overhead across high-throughput scheduling and streaming paths.
- Consistent, functional programming style for collection composition and iterator traversal.
- High-performance sequence operations implemented via native runtime iterator primitives.

Negative:

- Iterators are exhausted upon single traversal, requiring defensive handling if multi-pass evaluation is needed.
- Inspecting iterator pipelines during debugging is less straightforward compared to inspecting materialized collections.

## Alternatives

- Intermediate list materialization using list comprehensions and collection concatenations (rejected)
  Rejected because: Allocates unnecessary intermediate collections in heap memory, causing performance degradation in scheduling loops and large stream processing
  When valid: Valid when collection size is strictly small and constant, and multiple read passes are mandatory
- Custom generator functions and manual iteration loops for every sequence manipulation (rejected)
  Rejected because: Increases code complexity and execution overhead compared to native iterator primitives provided by the standard library
  When valid: Valid when sequence progression requires custom stateful transformation logic not expressible with standard iterator combinators

## Risks

- Accidental iterator exhaustion when passing an iterator to multiple consumers expecting fresh sequences
  Mitigation: Enforce explicit single-pass consumption contracts and convert to collections only at boundary interfaces when multi-pass access is verified as necessary
  Owner: engineering team
- Infinite iteration loops when applying itertools functions to unbounded cycle generators without termination guards
  Mitigation: Require explicit slicing or condition termination guards when working with potentially infinite streams
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
- Ensure iterator consumer functions do not assume sequence length or indexability unless explicitly verified.
- Pair stream slicing with boundary size checks to guarantee deterministic termination of unbounded generators.

## Continuation Context

Verify commands:

- # Discover the test runner from repository configuration and run the test suite for core stream and scheduling pipelines
- # Discover the linter and type checker from project configuration and verify compliance across all core modules

Accept when:

- All unit and integration tests covering scheduling loops, log stream accumulation, and timetable evaluation pass without errors.
- Static analysis and type checks report zero violations across modules implementing iterator pipelines.

## Enforcement

- Verified by: Automated continuous integration test suites and static type checking.
- Verified by: Peer code review verifying iterator single-pass consumption and absence of unneeded collection materialization.
- Violation handling: Pull requests introducing intermediate collection allocations in scheduling hot paths will be blocked until refactored to use lazy iterators.
- Exception process: Submit an architectural exception request detailing profiling evidence where collection materialization outperforms iterator evaluation.
