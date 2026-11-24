---
version: 2
name: 13be2151-bdf8-4be3-8387-4d991636ad36-structured-logging-via-structlog-consumers-discover-project-dependency-resolution-lock
description: "Structured Logging via structlog: Consumers Discover Project Dependency Resolution Lock"
metadata:
  actual:
    adr_id: 13be2151-bdf8-4be3-8387-4d991636ad36
    status: accepted
    candidate_group_id: 17f4104d-5c62-49e6-87ad-e62173be120f
    evidence:
      source: deep
  scope:
    root: kubernetes-tests
    paths:
      - lang_sdk/**
      - "!lang_sdk/go_example/**"
      - "!lang_sdk/java_example/**"
---

# Structured Logging via structlog: Consumers Discover Project Dependency Resolution Lock

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `kubernetes-tests`

Applies to:
- `lang_sdk/**`

Excludes:
- `lang_sdk/go_example/**`
- `lang_sdk/java_example/**`

## Context

- Components executing operational workflows and staging routines require consistent diagnostics to monitor execution and diagnose failures
- Unstructured console output across operational scripts obscures contextual metadata and prevents automated log parsing
- Standardizing structured logger instantiation provides uniform origin attribution and structured event emission across modules

## Problem Statement

Operational scripts and SDK utilities require structured and queryable logging to trace execution events, but relying on unstructured stream writes or inconsistent logger initialization obscures context and hinders automated log analysis.

## Decision

- **R-L-001** MUST: Consumers MUST discover the project dependency resolution lock artifact and verify the exact resolved version of structlog before implementing logging integrations.

## Policy Block

In scope:

- Operational modules, SDK utilities, and staging routines requiring runtime diagnostic event emission

Out of scope:

- Standalone zero-dependency bootstrap scripts with no access to external package ecosystems

Exceptions:

- **EXC-46-001** A module runs in an isolated bootstrap context where third-party libraries cannot be loaded
  Approval: Architecture review board
  Documentation: Document the fallback logging mechanism and isolation rationale within the module header

## Rationale

- Adopting structlog establishes consistent structured key-value diagnostic emission across operational routines
- Binding loggers via structlog.get_logger with explicit module names ensures proper namespace attribution in centralized log collectors
- Structured key-value event payloads facilitate automated indexing, log parsing, and operational alerting

## Consequences

Positive:

- Emits operational events as structured key-value data suitable for automated ingestion and querying
- Ensures consistent logger origin attribution through module-scoped logger naming
- Eliminates log parsing ambiguity inherent in unstructured string outputs

Negative:

- Introduces an external dependency requirement on structlog across operational execution environments
- Requires developers to adopt structured key-value event logging conventions rather than ad-hoc string formatting

## Alternatives

- Standard library logging without structured formatting (rejected)
  Rejected because: Standard library logging produces unstructured text by default and requires extensive custom formatting to achieve structured key-value output
  When valid: Permissible in isolated environments where external dependencies cannot be resolved
- Direct standard output and standard error stream writes (rejected)
  Rejected because: Direct stream printing lacks severity levels, contextual key-value metadata, and module namespace attribution
  When valid: Permissible only during early bootstrap routines prior to logger initialization

## Risks

- Inconsistent logging calls mixing formatted text strings with structured key-value arguments
  Mitigation: Enforce structured event logging conventions through static analysis and linting checks
  Owner: Engineering team
- Version drift between development and execution environments causing logging API incompatibilities
  Mitigation: Verify exact locked versions using repository dependency resolution artifacts prior to deployment
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
- Instantiate module-level loggers by calling structlog.get_logger with logger_name set to the module name identifier
- Pass contextual operational data as structured key-value arguments rather than embedding values inside message strings

## Continuation Context

Verify commands:

- Discover the repository static analysis and linting verification scripts and execute them to verify structlog logger initialization compliance.
- Discover and execute the project automated test suites to confirm that structured logging calls execute without schema or runtime errors.

Accept when:

- All logger instances in target modules are initialized through structlog.get_logger with logger_name configured to the module namespace
- Project verification and static analysis routines pass without logging-related violations

## Enforcement

- Verified by: Automated static analysis checks in continuous integration pipelines
- Verified by: Peer code review validation during change integration
- Violation handling: Continuous integration builds fail when unapproved logging APIs or direct stream writes are detected
- Violation handling: Code review approval is withheld until logging calls conform to structured logging rules
- Exception process: Submit an architectural review request documenting the technical constraints preventing structlog adoption
- Exception process: Obtain formal authorization from the architecture review board prior to merging fallback logging implementations
