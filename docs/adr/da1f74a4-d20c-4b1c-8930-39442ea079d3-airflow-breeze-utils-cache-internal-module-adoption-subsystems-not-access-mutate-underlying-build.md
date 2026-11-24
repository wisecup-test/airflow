---
version: 2
name: da1f74a4-d20c-4b1c-8930-39442ea079d3-airflow-breeze-utils-cache-internal-module-adoption-subsystems-not-access-mutate-underlying-build
description: "airflow_breeze.utils.cache Internal Module Adoption: Subsystems Not Access Mutate Underlying Build"
metadata:
  actual:
    adr_id: da1f74a4-d20c-4b1c-8930-39442ea079d3
    status: accepted
    candidate_group_id: cfb6ad1d-6ce2-487e-aac2-84a1dc3ecabd
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/**
      - breeze/src/airflow_breeze/**
      - registry/**
---

# airflow_breeze.utils.cache Internal Module Adoption: Subsystems Not Access Mutate Underlying Build

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `airflow_perf/**`
- `breeze/src/airflow_breeze/**`
- `registry/**`

## Context

- Developer environment tooling and container build orchestration workflows require state preservation across distinct command executions.
- Recomputing build signatures, checking artifact freshness, and inspecting console display preferences on every CLI invocation introduces latency and redundant operations.
- A unified internal module contract was required to standardize local cache reading, writing, and validation across disconnected subsystems.

## Problem Statement

Subsystems within the developer command-line suite and build workflow need to persist and verify execution parameters, build states, and configuration flags between invocations without introducing distributed storage dependencies or unmanaged, ad-hoc file writes across disparate working directories.

## Decision

- **R-LM-001** MUST_NOT: Subsystems MUST_NOT access or mutate the underlying build cache directory directly through arbitrary file system primitives.

## Policy Block

In scope:

- Internal developer environment utilities, CLI subcommands, and build orchestration routines managing execution state or parameter persistence.

Out of scope:

- Core runtime execution pathways outside developer tooling, such as production task orchestration and production worker execution.

## Rationale

- Evidence demonstrates multi-component reliance on airflow_breeze.utils.cache for coordinating state across image refresh handlers, console presentation, and build parameter checks.
- Encapsulating cache discovery, path resolution, and parameter validation inside a single internal module standardizes error handling and avoids duplicated path manipulation.
- Abstracting cache state management provides a clear interface boundary between command execution logic and local disk persistence.

## Consequences

Positive:

- Standardizes parameter persistence across disparate build and CLI utility modules.
- Reduces code duplication for file existence checks, content retrieval, and value validation.
- Enables centralized cache directory management and consistent cache eviction handling.

Negative:

- Couples developer tooling state management to local file system availability and directory permissions.
- Requires callers to conform strictly to key naming conventions to prevent cache key collisions.

## Alternatives

- Ad-hoc file system writes directly within individual utility modules (rejected)
  Rejected because: Leads to fragmented cache directory locations, duplicated validation logic, and high risk of inconsistent file handling across commands.
  When valid: Only viable in isolated single-file demonstration scripts that have no dependencies on shared developer workflows.
- External key-value daemon or remote metadata store (rejected)
  Rejected because: Introduces unnecessary runtime dependencies, latency overhead, and setup complexity for local developer workstation tasks.
  When valid: When state must be shared synchronously across distributed workers in a multi-node cluster.

## Risks

- Cache corruption or stale cache entries causing unexpected build behavior.
  Mitigation: Enforce value validation via read_and_validate_value_from_cache and provide clean touch_cache_file update routines.
  Owner: tooling engineering team
- Cache key collisions between unrelated subcommands.
  Mitigation: Establish distinct, descriptive parameter naming conventions enforced through code review and unit tests.
  Owner: tooling engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Consumers must ensure cache directory permissions allow reading and writing in local execution environments.
- Callers should implement defensive fallbacks when cache files are missing or unreadable to allow graceful degradation.

## Continuation Context

Verify commands:

- Discover the project test runner configuration from the repository manifest and execute test suites targeting developer utility modules.
- Discover the repository static analysis and linting configurations and execute style checks against internal utility packages.

Accept when:

- All unit tests exercising cache reading, writing, and validation contracts pass successfully without file system access errors.
- Static type checking and architectural boundary checks confirm that no modules bypass the public cache contract.

## Enforcement

- Verified by: Automated continuous integration test suites exercising caching utility contracts.
- Verified by: Peer code review verifying adherence to the centralized cache module interface.
- Violation handling: Pull requests bypassing the cache module in favor of direct file system mutation will be blocked.
- Violation handling: Static analysis violations or test failures will fail automated verification pipelines.
- Exception process: Exceptions require documented justification submitted to and approved by the tooling maintainers.
