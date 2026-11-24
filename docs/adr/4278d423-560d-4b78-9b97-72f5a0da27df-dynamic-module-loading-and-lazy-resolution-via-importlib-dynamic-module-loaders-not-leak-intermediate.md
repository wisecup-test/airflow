---
version: 2
name: 4278d423-560d-4b78-9b97-72f5a0da27df-dynamic-module-loading-and-lazy-resolution-via-importlib-dynamic-module-loaders-not-leak-intermediate
description: "Dynamic Module Loading and Lazy Resolution via importlib: Dynamic Module Loaders Not Leak Intermediate"
metadata:
  actual:
    adr_id: 4278d423-560d-4b78-9b97-72f5a0da27df
    status: accepted
    candidate_group_id: 8cdd2f7d-87b1-48fb-a69c-19765ef8e67a
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Dynamic Module Loading and Lazy Resolution via importlib: Dynamic Module Loaders Not Leak Intermediate

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Core framework components, serialization engines, and workflow definition loaders require dynamic loading of user artifacts and extension modules at runtime.
- Static top-level imports of optional extensions or distributed execution handlers introduce circular import cycles and substantial boot-time overhead.
- Ingestion subsystems must parse and execute arbitrary external source definitions while isolating global module state and capturing compilation errors cleanly.
- Deprecation transitions require dynamic attribute redirection to maintain compatibility across module reorganizations.

## Problem Statement

Static imports in complex modular frameworks lead to severe startup degradation, circular dependency knots, and an inability to dynamically ingest user-defined definitions and pluggable serialization handlers without hardcoding all potential dependencies into the core runtime.

## Decision

- **R-LM-001** MUST_NOT: Dynamic module loaders MUST_NOT leak intermediate or transient module entries into global namespace tables upon execution failure, and MUST remove failed registrations during cleanup handlers.

## Policy Block

In scope:

- All core subsystems executing dynamic module loading, pluggable component dispatch, or runtime user code ingestion.
- Subsystems implementing lazy attribute redirection and backward compatibility deprecation warnings.

Out of scope:

- Standard internal utility modules with static, well-defined dependency graphs.
- Static type-checking signatures and declarations evaluated purely at static analysis time.

## Rationale

- Direct use of importlib and its machinery provides standardized, robust primitives for dynamic module loading, caching, and execution.
- Isolating dynamic source loading behind importlib.machinery.SourceFileLoader and importlib.util ensures consistent specification handling and predictable error boundaries.
- Programmatic module redirection via importlib.import_module within dynamic attribute lookups decouples architectural evolution from breaking public interfaces.

## Consequences

Positive:

- Decouples core framework startup performance from heavy plugin or subsystem dependencies via deferred loading.
- Isolates user-provided artifact loading into explicit module specifications without requiring static installation.
- Enables transparent backward compatibility and deprecation paths through dynamic module attribute redirection.

Negative:

- Shifts import syntax and missing dependency failures from initial application boot time to dynamic runtime execution.
- Requires strict lifecycle management of global module registries to prevent memory retention or namespace pollution.
- Increases complexity in static analysis and development tooling when tracing dynamically loaded dependencies.

## Alternatives

- Static hardcoded imports across all module registries and serialization boundaries (rejected)
  Rejected because: Creates tight coupling, circular import dependencies, and unacceptable startup latency by importing heavy dependencies unconditionally.
  When valid: Valid in small standalone applications with static dependency graphs and no external plugin requirements.
- Direct source string execution via builtin eval and exec without module specifications (rejected)
  Rejected because: Bypasses standard module cache semantics, breaks debugging stack traces, and exposes execution to namespace collision.
  When valid: Valid only for isolated expression evaluation in restricted sandboxes without modular code structures.

## Risks

- Runtime import failures can remain undetected until specific execution branches or user artifacts are accessed.
  Mitigation: Implement structured exception handling around import operations to capture and record diagnostic tracebacks without halting the host process.
  Owner: engineering team
- Dynamic loading of repeated or malformed files can pollute the global module cache or cause memory leakage.
  Mitigation: Ensure cleanup routines systematically purge intermediate or aborted module keys from sys.modules during exception handling.
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
- When executing external source definitions via dynamic loaders, construct deterministic module names and ensure stale entries are removed from the global module registry prior to re-execution.
- Encapsulate dynamic attribute lookups in module-level dispatchers to support deprecated paths while raising designated warning categories.

## Continuation Context

Verify commands:

- git grep -n -E 'importlib\\.(import_module|machinery|util)'
- git grep -n 'SourceFileLoader'
- git grep -n 'exec_module'

Accept when:

- All dynamic module loading routines utilize importlib APIs and pass automated test suites for module isolation.
- Global module cache state is verified to be cleanly managed and restored during module loading failures.
- Dynamic attribute redirection correctly triggers documented deprecation warnings without unhandled attribute errors.

## Enforcement

- Verified by: Static analysis linting rules verifying importlib usage patterns across core subsystems.
- Verified by: Automated integration test suites validating dynamic module isolation and exception handling.
- Verified by: Peer review on pull requests touching runtime loading, serialization, or plugin dispatch mechanisms.
- Violation handling: Automated pull request checks fail on non-standard dynamic loading or unmanaged module cache manipulation.
- Violation handling: Code revisions required to align dynamic import structures with canonical importlib patterns prior to merge.
- Exception process: Submit an architecture exception request detailing why standard importlib specifications cannot satisfy the runtime requirement.
- Exception process: Secure explicit approval from the core framework architecture review group before introducing non-standard loading mechanisms.
