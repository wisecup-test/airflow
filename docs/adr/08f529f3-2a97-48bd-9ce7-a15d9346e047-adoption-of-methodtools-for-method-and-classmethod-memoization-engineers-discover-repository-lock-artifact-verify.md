---
version: 2
name: 08f529f3-2a97-48bd-9ce7-a15d9346e047-adoption-of-methodtools-for-method-and-classmethod-memoization-engineers-discover-repository-lock-artifact-verify
description: "Adoption of methodtools for Method and Classmethod Memoization: Engineers Discover Repository Lock Artifact Verify"
metadata:
  actual:
    adr_id: 08f529f3-2a97-48bd-9ce7-a15d9346e047
    status: accepted
    candidate_group_id: b720d11a-8a18-4088-b4b5-724d680200f7
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of methodtools for Method and Classmethod Memoization: Engineers Discover Repository Lock Artifact Verify

Date: 2025-02-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Domain models and serialization definitions repeatedly compute structural properties such as mapped task counts and weight rule sets during scheduling cycles.
- Standard library function-level caching decorators retain references to bound instances, creating memory leaks and inhibiting garbage collection in serialized graph structures.
- The codebase adopts an external descriptor-aware caching module to ensure cache lifecycles align with instance and class boundaries.

## Problem Statement

Standard library function-level LRU caching attaches cached references to the decorator closure rather than the instance, which either causes memory leaks by preventing the garbage collection of bound instances or fails on class methods. The system requires method-aware and classmethod-aware caching across serialized graph nodes and validation helpers without compromising memory management.

## Decision

- **R-LM-001** MUST: Engineers MUST discover the repository lock artifact and verify the exact resolved version of the methodtools library prior to introducing or updating caching decorators.

## Policy Block

In scope:

- Instance methods and class methods on serialization nodes, graph components, and task configuration classes that require memoized property calculations.

Out of scope:

- Module-level standalone functions with no class or instance binding where standard library caching decorators are sufficient.
- Short-lived local functions where caching overhead exceeds calculation cost.

## Rationale

- The codebase relies on repeated queries against structural DAG nodes and mapped counts; decorator-based method caching eliminates redundant recalculations during scheduling runs.
- Using methodtools ensures that method caches are bound directly to the target instance or class descriptor, preventing the memory retention issues inherent in standard library function caches applied to bound methods.
- Static evidence demonstrates consistent application of methodtools.lru_cache across serialized definitions and domain helper classes.

## Consequences

Positive:

- Eliminates redundant calculations for graph nodes and validation collections during parsing and execution cycles.
- Avoids memory leaks by properly binding cache storage to instance and class descriptors rather than global decorator state.
- Standardizes declarative memoization syntax across core serialization and task definition models.

Negative:

- Introduces an external dependency for behavior closely related to the standard library.
- Requires engineers to distinguish between method-level and function-level caching conventions across modules.

## Alternatives

- Standard library functools.lru_cache on instance methods (rejected)
  Rejected because: Retains references to instance objects in closure dictionaries, preventing garbage collection and leading to memory leaks in long-running processes.
  When valid: Valid only for module-level standalone functions that do not bind to instance or class descriptors.
- Manual dictionary caching on instance attributes (rejected)
  Rejected because: Increases boilerplate code, introduces manual invalidation risks, and bypasses declarative decorator syntax.
  When valid: Valid when custom cache eviction algorithms or external persistent backends are explicitly required.

## Risks

- Inappropriate cache sizing leading to unexpected memory consumption under large graph expansions.
  Mitigation: Enforce explicit maxsize parameters on all method caches via code reviews and static checks.
  Owner: engineering team
- Accidental use of standard library function caching on methods during new feature development.
  Mitigation: Include static import checks to flag prohibited decorator patterns on class definitions.
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
- When decorating instance methods that return scalar counts or static configurations, set the cache size parameter to one to retain only the most recent state.
- When decorating class methods that return immutable domain enumerations or validation collections, configure the cache to unbounded capacity.

## Continuation Context

Verify commands:

- Discover the repository test runner script and execute unit tests targeting serialization and task modules.
- Discover the repository static analysis and linting scripts to verify adherence to method caching conventions.

Accept when:

- All unit tests covering serialization and task node caching pass without regression.
- Static analysis checks confirm no standard library function caches decorate bound instance methods.

## Enforcement

- Verified by: Continuous integration test suites verifying serialization and task execution behavior.
- Verified by: Automated static analysis checks and peer reviews of proposed caching decorators.
- Violation handling: Pull requests applying standard function caching decorators to bound instance methods will be rejected until updated to the required decorator.
- Exception process: Exceptions require approval from the technical architecture group with memory profiling data demonstrating no reference retention leaks.
