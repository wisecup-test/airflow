---
version: 2
name: 6fe1925a-78ce-4ac2-b32d-6947338e211a-functools-module-adoption-for-in-memory-memoization-and-callable-composition-functions-that-memoize-results-functools-lru
description: "functools Module Adoption for In-Memory Memoization and Callable Composition: Functions That Memoize Results Functools Lru"
metadata:
  actual:
    adr_id: 6fe1925a-78ce-4ac2-b32d-6947338e211a
    status: accepted
    candidate_group_id: 45b55e3c-9e6b-4f15-8d89-10501bd1423e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# functools Module Adoption for In-Memory Memoization and Callable Composition: Functions That Memoize Results Functools Lru

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Workflow scheduling and task dependency evaluation execute high-frequency iterative loops that compute identical upstream states, mapped instance counts, and serialized configurations.
- Redundant queries against the persistence store and repeated dictionary traversals during task execution evaluation create significant processing overhead and database contention.
- In-memory memoization is required to bound query frequency and attribute recomputation to single execution ticks or object lifetimes.
- Consistent application of standard functional abstractions and caching decorators avoids fragmented, ad-hoc state-caching implementations across subsystems.

## Problem Statement

High-frequency workflow evaluation routines and serialized model inspections frequently recompute invariant attributes and execute identical database count queries within the same scheduling cycle. Without a standardized, lifecycle-bound caching and functional utility strategy, disparate components implement custom mutable dictionaries or incur severe database load, introducing risks of memory leaks, state desynchronization, and degraded scheduler throughput.

## Decision

- **R-LM-001** MUST: Functions that memoize results using functools lru_cache MUST specify an explicit maximum cache size or be scoped within local closures to prevent unbounded memory growth.

## Policy Block

In scope:

- Core workflow dependency checking, trigger rule evaluations, and task state computations.
- Serialization and deserialization definitions for operators, task groups, and directed acyclic graphs.
- Lifecycle monitoring, job heartbeats, and provider manager registry evaluations.

Out of scope:

- State mutations that require immediate cross-process visibility or distributed synchronization.
- Volatile operational data whose freshness must reflect immediate external system modifications.

Exceptions:

- **EX-20-001** A high-concurrency background runner requires a dedicated thread-safe external cache or specialized data structure with fine-grained eviction policies.
  Approval: Architecture Review Board
  Documentation: Document the cache eviction semantics and benchmark comparison against standard library decorators in the component design specification.

## Rationale

- The functools module provides standardized, well-tested functional programming primitives and caching descriptors that prevent redundant computation without introducing third-party dependencies.
- Applying lru_cache within localized query closures restricts database query execution to at most once per evaluation cycle, drastically reducing query amplification on mapped task graphs.
- Using cached_property on immutable serialized structures defers expensive link and attribute resolutions until point of access while guaranteeing single computation per model lifecycle.

## Consequences

Positive:

- Significant reduction in database query volume during task instance expansion and trigger dependency resolution.
- Consistent caching semantics across serialization definitions, API services, and core orchestration components.
- Elimination of custom, bug-prone dictionary caching logic and ad-hoc memoization flags.

Negative:

- Risk of subtle memory retention if cached closures or method decorators inadvertently capture references to large object graphs or database sessions.
- Potential for stale attribute reads if cached_property is mistakenly applied to mutable attributes that vary over the object lifetime.

## Alternatives

- Custom dictionary-based caching with manual invalidation flags (rejected)
  Rejected because: Manual dictionary caching introduces boilerplate, increases maintenance overhead, and frequently causes memory leaks due to missing eviction logic.
  When valid: Valid only when non-standard cache eviction keys or custom eviction policies cannot be expressed through standard decorators.
- External distributed caching layer for all intermediate scheduler calculations (rejected)
  Rejected because: Network latency and serialization overhead to an external cache exceed the computation time of localized task dependency checks.
  When valid: Valid for cross-host state coordination and shared cluster metadata rather than process-local evaluation closures.

## Risks

- Unbounded cache growth in long-running processes causing memory exhaustion.
  Mitigation: Mandate explicit maxsize limits on process-level caches and restrict query caches to local function closures.
  Owner: Core Engineering Team
- Stale data propagation across database transactions due to improper cache scoping.
  Mitigation: Enforce that session-dependent cached closures are instantiated within the transaction boundary and never shared across transaction lifecycles.
  Owner: Core Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- When using lru_cache for database query optimization inside dependency checks, define the cached function as an inner closure within the evaluation method so that the cache is discarded when the evaluation finishes.
- Use cached_property exclusively on immutable or read-only properties of data transfer objects and serialized definitions where property values remain constant after initialization.

## Continuation Context

Verify commands:

- Discover the repository static analysis script from the project configuration and execute it to verify compliance of caching decorators and closure scopes.
- Discover the repository test execution command from the build manifest and run the dependency resolution and serialization test suite to confirm cache correctness.

Accept when:

- Static analysis checks pass with zero violations regarding unbounded cache usage on stateful classes.
- All dependency evaluation and serialization automated test suites execute and pass without cache-related memory leaks or stale state assertions.

## Enforcement

- Verified by: Automated static analysis checks in the continuous integration pipeline scanning for improper cache decorator usage.
- Verified by: Peer code review verifying that cached closures are appropriately scoped to transaction lifetimes.
- Violation handling: Pull requests containing unbounded global caches or improperly scoped session-bound memoization will be blocked in code review.
- Violation handling: Static analysis violations in continuous integration will fail the build and require remediation prior to merge.
- Exception process: Exceptions must be submitted via an architecture review request detailing why standard functional decorators are insufficient and demonstrating memory safety.
