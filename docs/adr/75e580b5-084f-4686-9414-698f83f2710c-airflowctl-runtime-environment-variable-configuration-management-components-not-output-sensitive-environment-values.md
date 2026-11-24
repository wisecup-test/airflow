---
version: 2
name: 75e580b5-084f-4686-9414-698f83f2710c-airflowctl-runtime-environment-variable-configuration-management-components-not-output-sensitive-environment-values
description: "airflowctl Runtime Environment Variable Configuration Management: Components Not Output Sensitive Environment Values"
metadata:
  actual:
    adr_id: 75e580b5-084f-4686-9414-698f83f2710c
    status: accepted
    candidate_group_id: 4facb53a-ebe2-4197-bb25-e8230965bd29
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/**
---

# airflowctl Runtime Environment Variable Configuration Management: Components Not Output Sensitive Environment Values

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `docs/**`
- `src/airflowctl/**`

## Context

- The airflowctl codebase coordinates CLI commands, authentication operations, documentation builds, and HTTP client communication across distinct execution contexts.
- Runtime execution boundaries require externalized configuration for debug flags, authentication tokens, API target environments, retry thresholds, and documentation generation modes.
- Direct dependency on environment variable sourcing allows dynamic overrides without modifying source files or committing sensitive values to revision control.

## Problem Statement

Distributed subsystems within airflowctl need to resolve operational configuration and sensitive credentials dynamically across varying execution environments without persisting sensitive state to disk or requiring hardcoded parameters.

## Decision

- **R-CEM-001** MUST_NOT: Components MUST NOT output sensitive environment values or resolved authentication tokens in unredacted diagnostic logs or terminal output.

## Policy Block

In scope:

- All runtime command handlers, API clients, and execution hooks within the package.
- All runtime settings governing debugging, authentication credentials, retry thresholds, and target environments.

Out of scope:

- Compile-time code generation scripts that run strictly outside the application execution lifecycle.
- Third-party plugins with isolated external configuration mechanisms.

Exceptions:

- **EXC-37-001** When execution occurs in sandbox or air-gapped runtimes where operating system environment variables are completely inaccessible.
  Approval: Lead Systems Architect
  Documentation: An architecture exception log detailing the local fallback configuration mechanism.

## Rationale

- Environment variable ingestion provides seamless integration with container runtimes, process schedulers, and continuous deployment runners.
- Standardizing on namespaced environment variables avoids collision with unrelated system variables while enabling straightforward runtime overrides.
- Decoupling authentication credentials and operational thresholds into environment sources preserves credential confidentiality and runtime adaptability.

## Consequences

Positive:

- Enables flexible runtime reconfiguration across diverse deployment targets without modifying code artifacts or configuration files.
- Facilitates non-interactive authentication and automation in continuous integration and remote orchestration pipelines.
- Provides clear precedence hierarchies between command flags, environment overrides, and package defaults.

Negative:

- Scattered environment lookups across command and client modules can lead to inconsistent default values if not routed through centralized accessor functions.
- Troubleshooting runtime behavior requires inspecting operating system environment state in addition to explicit invocation parameters.
- Improper handling of sensitive environment values in debugging handlers could inadvertently expose secrets in diagnostic output.

## Alternatives

- Static configuration files exclusively for runtime execution settings (rejected)
  Rejected because: Precludes dynamic container orchestration overrides, automated testing adjustments, and secure injection of sensitive tokens into CLI processes without writing secrets to persistent disk.
  When valid: When deploying in immutable infrastructure where runtime environment variables are prohibited by policy.
- Direct global variable mutation across modules without environment resolution helpers (rejected)
  Rejected because: Creates tight coupling across subsystems, eliminates auditability of configuration origins, and prevents external override capabilities.
  When valid: When developing self-contained embedded routines that do not interact with operating system environments or external services.

## Risks

- Inconsistent default fallback values across different call sites for the same configuration variable.
  Mitigation: Encapsulate environment lookups behind domain-specific configuration helpers with explicit fallback defaults.
  Owner: engineering team
- Accidental exposure of sensitive tokens in debug logs or exception traces.
  Mitigation: Enforce strict sanitization and header-level masking on diagnostic logging handlers.
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
- Centralize environment variable definitions and key constants within designated configuration modules to prevent drift in variable names.
- Ensure test suites mock environment access cleanly to avoid state pollution between test executions.
- Mask or suppress sensitive environment values during logging and command debugging output.

## Continuation Context

Verify commands:

- Discover the project test runner and execute the automated test suite covering configuration and client runtime modules.
- Discover and run static analysis rules that check for standardized environment variable access patterns across the repository.

Accept when:

- Environment variable reads across client and command boundaries pass automated verification without direct undeclared access.
- Configuration precedence correctly prioritizes explicit command arguments over environment variables with fallback to internal defaults.
- Automated test suites pass with isolated environment variables verifying repeatable behavior.

## Enforcement

- Verified by: Static analysis checks verifying environment access calls adhere to established namespace conventions.
- Verified by: Automated test suites running across isolated environment variable states.
- Verified by: Peer code reviews inspecting all runtime configuration resolution routines.
- Violation handling: Pull requests containing unnamespaced or undeclared environment variable access will fail automated quality gates.
- Violation handling: Direct disk persistence of tokens or secrets discovered in place of environment sourcing will be rejected immediately.
- Violation handling: Violations detected in mainline branches require immediate remediation patches or reversion.
- Exception process: Submit an architecture review proposal detailing the technical limitation preventing standard environment configuration sourcing.
- Exception process: Obtain explicit sign-off from the technical leads before implementing alternative configuration ingestion paths.
- Exception process: Document the approved deviation in module documentation with a scheduled deprecation date.
