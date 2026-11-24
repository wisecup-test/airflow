---
version: 2
name: a57f5959-b9e2-4793-8558-5d68aa5ff3f1-hierarchical-logger-resolution-via-logging-getlogger-when-managing-dependencies-consumers-inspect-repository
description: "Hierarchical Logger Resolution via logging.getLogger: When Managing Dependencies Consumers Inspect Repository"
metadata:
  actual:
    adr_id: a57f5959-b9e2-4793-8558-5d68aa5ff3f1
    status: accepted
    candidate_group_id: 1080f633-225d-40cc-b490-c069853c8824
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/docs/**
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Hierarchical Logger Resolution via logging.getLogger: When Managing Dependencies Consumers Inspect Repository

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/docs/**`
- `src/sphinx_exts/**`
- `src/tests_common/test_utils/**`

## Context

- Subsystem components, documentation extension modules, and test harness utilities require diagnostic observability and telemetry output.
- Direct standard output streaming and root logger usage cause uncoordinated message interleaving, test log pollution, and inability to filter log levels by subsystem.
- A standardized logging hierarchy enables decoupled log level configuration, granular message filtering, and isolated test stream capture.

## Problem Statement

Without a standardized logging convention, disparate modules risk writing unformatted messages directly to standard streams or attaching handlers to the unnamespaced root logger. This causes log message collisions, prevents fine-grained verbosity controls across distinct subsystems, and interferes with automated test assertions that inspect standard output streams.

## Decision

- **R-L-001** MUST: When managing dependencies, consumers MUST inspect the repository dependency manifest and lock artifacts to determine the exact locked environment specification before implementation.

## Policy Block

In scope:

- All source modules within documentation extensions, performance benchmarking suites, and testing utility frameworks requiring log generation or stream inspection.

Out of scope:

- Static front-end client assets that execute outside the runtime environment.

Exceptions:

- **EXC-46-001** Direct standard stream redirection is explicitly required for capturing low-level binary streams or subprocess execution.
  Approval: Architecture Review Board
  Documentation: Document exception in the relevant test harness interface docstring.

## Rationale

- Module-scoped logger instantiation ensures that every log record carries its hierarchical provenance, enabling selective log filtering and custom level overrides per subsystem.
- Standardizing on the logging library interface prevents uncoordinated direct writes to standard streams, simplifying integration with stream redirection contexts.
- Explicit handler manipulation during test execution protects log assertions from cross-test contamination and ensures deterministic teardown.

## Consequences

Positive:

- Granular log filtering and level adjustment across independent subsystems without modifying source code.
- Predictable log record provenance facilitating debugging in development and automated testing runs.
- Clean isolation between standard stream redirection utilities and log handler output streams.

Negative:

- Developers must consistently acquire loggers per module rather than using unstructured console print operations.
- Test suites that isolate or capture standard streams must manage handler registration lifecycles and cleanup routines.

## Alternatives

- Direct Standard Output and Error Printing (rejected)
  Rejected because: Lacks log level filtering, module context metadata, and structured handler formatting while causing collisions during concurrent execution.
  When valid: One-off command-line debug scripts operating outside the shared repository architecture.
- Global Root Logger Direct Invocation (rejected)
  Rejected because: Applies uniform configuration across all packages, preventing targeted verbosity filtering and creating test handler side effects.
  When valid: Single-file command line tools without subpackages or distinct operational domains.

## Risks

- Handler accumulation or stream leakage during repeated test suite execution when stream handlers are modified dynamically.
  Mitigation: Ensure test context managers encapsulate handler restoration within exit callbacks and finalizers.
  Owner: engineering team
- Inconsistent module naming conventions leading to broken hierarchy navigation in logger configuration trees.
  Mitigation: Enforce module-level logger instantiation via static linters and automated code inspection checks.
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
- Instantiate loggers at module scope immediately after imports, referencing the current module namespace identifier.
- When designing stream capture fixtures, register handler removal and restoration logic inside context manager callbacks to guarantee cleanup upon test termination.

## Continuation Context

Verify commands:

- Discover the repository test runner script from the configuration files and execute the test harness suite to verify logging capture and stream isolation behavior.
- Inspect source modules across the repository using the project static analysis and linting routines to confirm that all diagnostic logging uses standard module logger instances.

Accept when:

- All modules instantiate loggers through standard library logger resolution with module-scoped names.
- Automated test suites pass without unhandled logger handler leaks or stream capture collisions.
- No raw print statements are detected in non-CLI internal utility modules.

## Enforcement

- Verified by: Automated continuous integration lint checks inspecting logger instantiation patterns.
- Verified by: Peer code review during pull request inspection.
- Violation handling: Pull requests containing unapproved direct stream printing or root logger invocations will fail verification gates and require remediation.
- Exception process: Exceptions must be submitted via architectural review with explicit justification for raw stream handling.
