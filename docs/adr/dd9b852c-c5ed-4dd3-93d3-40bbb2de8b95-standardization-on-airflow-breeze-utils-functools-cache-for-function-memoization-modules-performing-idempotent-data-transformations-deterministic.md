---
version: 2
name: dd9b852c-c5ed-4dd3-93d3-40bbb2de8b95-standardization-on-airflow-breeze-utils-functools-cache-for-function-memoization-modules-performing-idempotent-data-transformations-deterministic
description: "Standardization on airflow_breeze.utils.functools_cache for Function Memoization: Modules Performing Idempotent Data Transformations Deterministic"
metadata:
  actual:
    adr_id: dd9b852c-c5ed-4dd3-93d3-40bbb2de8b95
    status: accepted
    candidate_group_id: 62a02ca6-f081-4f03-8875-4b7197f5fe67
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Standardization on airflow_breeze.utils.functools_cache for Function Memoization: Modules Performing Idempotent Data Transformations Deterministic

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`

## Context

- Multiple utility and constant definition modules perform repetitive, deterministic operations including configuration parsing and platform integration mapping.
- Recomputing these static or idempotent values repeatedly across development workflows introduces latency and redundant disk or system access.
- The codebase standardizes on the internal module airflow_breeze.utils.functools_cache to provide uniform in-memory function memoization across shared modules.

## Problem Statement

Internal developer utilities and constant definitions frequently perform idempotent computations such as parsing workspace configuration tables and resolving platform integration matrices. Without a centralized memoization mechanism, modules either implement disparate custom caching solutions or incur repeated execution costs, leading to inconsistent cache invalidation behavior and increased overhead during workflow execution.

## Decision

- **R-LM-001** MUST: Modules performing idempotent data transformations or deterministic system queries MUST use airflow_breeze.utils.functools_cache for function memoization rather than implementing bespoke cache dictionaries or disparate caching mechanisms.

## Policy Block

In scope:

- Utility modules and constant definition files within the developer tooling framework.
- Pure, deterministic functions processing configuration data, integration lists, or system environment lookups.

Out of scope:

- Functions that interact with network endpoints or mutate external filesystem resources.
- Functions with unbounded cardinality of unique arguments that could cause unconstrained memory growth.

Exceptions:

- **EXC-20-001** A function requires custom time-based or size-bounded cache eviction policies not supported by the standard memoization module.
  Approval: Tooling maintainers review and architectural consensus
  Documentation: Document rationale and eviction semantics directly above the custom cache definition

## Rationale

- Centralizing memoization through airflow_breeze.utils.functools_cache guarantees uniform caching semantics across disparate modules and simplifies dependency tracking.
- Reusing a single internal caching module prevents duplication of caching boilerplate and ensures consistent handling of immutable constants.
- Static configurations and platform mappings evaluated once per process execution avoid repeated file system access and matrix computations.

## Consequences

Positive:

- Reduces latency during developer tooling execution by eliminating repeated parsing and platform resolution.
- Eliminates fragmented caching implementations across utility and constant definition modules.
- Enforces deterministic function design by requiring hashable, immutable parameters.

Negative:

- Retains cached results in process memory for the lifetime of execution, increasing memory footprint if applied inappropriately.
- Prevents observation of external filesystem or configuration changes occurring after initial function invocation within a running process.

## Alternatives

- Ad-hoc module-level global dictionaries and manual lookup checks (rejected)
  Rejected because: Manual caching boilerplate leads to inconsistent thread safety, code duplication, and error-prone invalidation across files.
  When valid: Never within standard developer tooling utilities.
- Direct un-memoized re-execution of configuration parsing and system queries on every invocation (rejected)
  Rejected because: Incurs redundant disk reads and repeated sorting or mapping operations across internal workflows.
  When valid: When function inputs or underlying filesystem configurations change continuously during process execution.

## Risks

- Unbounded memory consumption if memoized functions receive high-cardinality dynamic inputs.
  Mitigation: Restrict memoization to pure functions with small, bounded domains of hashable inputs.
  Owner: Engineering team
- Stale configuration values if the underlying configuration source changes during a long-running process.
  Mitigation: Limit caching to process-scoped invocations or provide explicit cache clearing hooks where long-running daemon processes require refresh.
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
- Import the caching decorator directly from airflow_breeze.utils.functools_cache when defining pure utility queries or static constants.
- Ensure all parameter types passed into cached functions are immutable tuples or primitives rather than mutable collections.

## Continuation Context

Verify commands:

- Discover and execute the project test suite runner to validate that memoized functions return expected results.
- Discover and execute the project linter and static analysis scripts to verify imports and syntax compliance.

Accept when:

- The project test suite passes completely with zero caching regressions or mutated state failures.
- Static analysis and import verification succeed without reporting unauthorized caching decorators or duplicate implementations.

## Enforcement

- Verified by: Continuous integration test suite execution verifying cached function correctness and determinism.
- Verified by: Peer code review auditing all new usages of the memoization decorator across developer tooling modules.
- Violation handling: Pull requests introducing ad-hoc caching implementations or applying memoization to impure functions are blocked until corrected.
- Exception process: Exceptions for custom eviction policies require written architecture rationale in the pull request and maintainer approval.
