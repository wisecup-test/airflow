---
version: 2
name: 46c322d8-d898-4bf7-8dcc-f1980ef39e4f-standard-logging-with-module-scoped-hierarchy-via-logging-getlogger-name-before-introducing-extending-logging-facilities-external
description: "Standard Logging with Module-Scoped Hierarchy via logging.getLogger(__name__): Before Introducing Extending Logging Facilities External"
metadata:
  actual:
    adr_id: 46c322d8-d898-4bf7-8dcc-f1980ef39e4f
    status: accepted
    candidate_group_id: b5e38043-d7e2-4d83-a8f1-5b26783b8a45
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - assign_cherry_picked_prs_with_milestone.py
      - prepare_bulk_issues.py
      - registry/**
      - stats/**
---

# Standard Logging with Module-Scoped Hierarchy via logging.getLogger(__name__): Before Introducing Extending Logging Facilities External

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `assign_cherry_picked_prs_with_milestone.py`
- `prepare_bulk_issues.py`
- `registry/**`
- `stats/**`

## Context

- Operational tooling scripts in the repository handle automated issue processing, milestone tracking, statistics calculation, and registry parameter extraction.
- Intermediate representation analysis identifies consistent module-level logger creation via the standard logging API across multiple maintenance utilities.
- Adopting hierarchical logger namespaces based on module paths enables granular filtering, diagnostic control, and decoupled log formatting.

## Problem Statement

Ad-hoc logging through global root logger calls or direct standard output printing creates uncoordinated diagnostic streams, prevents fine-grained verbosity control across individual utility modules, and complicates automated log capture during repository maintenance operations.

## Decision

- **R-L-001** MUST: Before introducing or extending logging facilities with external runtime or framework logging extensions, the consumer MUST inspect the project dependency manifest and resolved lock artifact to confirm compatible runtime versions.

## Policy Block

In scope:

- Developer tooling, maintenance scripts, and automation utilities that produce diagnostic, operational, or status logs.

Out of scope:

- User-facing console output intended strictly for terminal formatting and interactive CLI dialogs where structural log hierarchy is unnecessary.

Exceptions:

- **EXC-46-001** A script functions as an interactive terminal interface using specialized terminal formatting protocols
  Approval: Lead tooling maintainer review
  Documentation: Document in the module header explaining the console stream isolation requirement

## Rationale

- Calling logging.getLogger(__name__) ensures that every log record is automatically tagged with the hierarchical module path, allowing per-module log level filtering without hardcoding logger strings.
- Standardizing on module-level logger instantiation isolates module diagnostics from global logging configuration while maintaining compatibility with project-wide log handlers.
- Evidence across operational scripts demonstrates an established convention of module-scoped logger acquisition that avoids logger collisions.

## Consequences

Positive:

- Hierarchical logger naming enables selective log level configuration per package and module.
- Prevents root logger pollution and channel namespace collisions across diverse automation tasks.
- Facilitates seamless integration with repository-wide log aggregators, formatters, and test execution harnesses.

Negative:

- Requires explicit logging handler configuration to avoid silent log record drops when modules execute as standalone entrypoints.
- Introduces boilerplate logger initialization statements at the top of every operational module.

## Alternatives

- Direct root logger invocations via module-level convenience logging calls (rejected)
  Rejected because: Pollutes the global logging namespace and prevents module-specific log level filtering and handler routing
  When valid: Throwaway single-file exploratory prototype scripts outside the repository codebase
- Hardcoded custom string channel names for individual utility modules (rejected)
  Rejected because: Introduces string duplication, lacks hierarchical relationship to package structure, and breaks when modules are refactored or moved
  When valid: Cross-cutting subsystem loggers that span multiple disparate modules under a single logical topic

## Risks

- Standalone script execution may produce no visible log output if root handlers are not initialized by the entrypoint.
  Mitigation: Ensure main CLI entrypoint functions configure default basic handlers or invoke centralized logging setup routines.
  Owner: tooling and automation engineering team
- Duplicate log records when both module loggers and root loggers attach separate output handlers.
  Mitigation: Enforce logger hierarchy propagation defaults and restrict handler attachment to application entrypoints.
  Owner: tooling and automation engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Instantiate the logger instance at the top of the module immediately following import statements, binding it to a module-scoped variable.
- Configure log handlers, stream sinks, and severity thresholds exclusively within main block execution guards or application entrypoint functions.

## Continuation Context

Verify commands:

- Discover and run the repository static analysis and linting validation script to inspect module-level logger instantiations.
- Discover the test runner from repository manifests and execute unit test suites covering operational utilities to verify diagnostic log emission.

Accept when:

- Static analysis confirms all operational utility modules instantiate loggers using logging.getLogger(__name__) without unapproved root logging calls.
- Automated tests for operational scripts pass with expected log events captured at configured severity levels.

## Enforcement

- Verified by: Automated static analysis checks in continuous integration workflows
- Verified by: Peer code review for pull requests modifying tooling or operational scripts
- Violation handling: Pull requests containing direct root logging calls or hardcoded logger channels will fail static validation and require remediation prior to merge.
- Exception process: Exceptions must be requested through code review with justification explaining interactive CLI constraints and approved by the tooling maintainer.
