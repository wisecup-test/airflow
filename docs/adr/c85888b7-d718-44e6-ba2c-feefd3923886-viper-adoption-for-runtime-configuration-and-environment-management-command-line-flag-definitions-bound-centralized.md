---
version: 2
name: c85888b7-d718-44e6-ba2c-feefd3923886-viper-adoption-for-runtime-configuration-and-environment-management-command-line-flag-definitions-bound-centralized
description: "Viper Adoption for Runtime Configuration and Environment Management: Command Line Flag Definitions Bound Centralized"
metadata:
  actual:
    adr_id: c85888b7-d718-44e6-ba2c-feefd3923886
    status: accepted
    candidate_group_id: 50f3417c-6a7c-4c41-9d8f-c6bc622af394
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/bundlev1server/impl/**
      - cmd/airflow-go-edge-worker/**
      - cmd/airflow-go-pack/**
      - edge/**
      - pkg/bundles/shared/**
      - pkg/config/**
      - pkg/worker/**
---

# Viper Adoption for Runtime Configuration and Environment Management: Command Line Flag Definitions Bound Centralized

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `bundle/bundlev1/bundlev1server/impl/**`
- `cmd/airflow-go-edge-worker/**`
- `cmd/airflow-go-pack/**`
- `edge/**`
- `pkg/bundles/shared/**`
- `pkg/config/**`
- `pkg/worker/**`

## Context

- Distributed worker runtimes, CLI utilities, and server plugins require access to structured configuration, environment overrides, and command flags.
- Direct, unstructured environment access across scattered packages leads to inconsistent default values, fragmented precedence handling, and difficult schema changes.
- Static analysis across seven runtime files demonstrates widespread adoption of Viper for typed configuration retrieval and flag binding, with targeted standard environment lookups reserved for bootstrapping.

## Problem Statement

Scattered and inconsistent configuration management across CLI binaries, worker execution engines, and server plugins creates fragmented precedence rules, unpredictable environment overrides, and unvalidated runtime parameters.

## Decision

- **R-CEM-001** SHOULD: Command line flag definitions SHOULD be bound to the centralized configuration registry during initialization to maintain layered configuration precedence.

## Policy Block

In scope:

- All runtime packages, worker services, CLI binaries, and plugin modules requiring configurable runtime parameters.

Out of scope:

- Static compile-time constants that do not vary across environments or deployments.

Exceptions:

- **EXC-37-001** Low-level process bootstrapping and binary discovery require initialization before the configuration subsystem is loaded.
  Approval: Runtime Architecture Lead
  Documentation: Document the justification and specific bootstrap keys in the component package documentation.

## Rationale

- Adopting Viper establishes a standard mechanism for configuration file discovery, structured unmarshaling, and command flag binding across all runtime binaries.
- Restricting direct environment queries prevents configuration drift while still accommodating low-level process bootstrapping requirements.
- Consistent use of typed getters and struct unmarshaling mitigates type conversion errors at runtime.

## Consequences

Positive:

- Provides unified configuration precedence handling across command-line flags, environment variables, and configuration files.
- Enables type-safe property retrieval and structured unmarshaling into domain models.
- Standardizes configuration access semantics across distributed worker runtimes and plugin server components.

Negative:

- Introduces an external dependency and runtime indirection into the configuration resolution path.
- Reliance on global configuration state can complicate concurrent unit testing if instances are not properly scoped.

## Alternatives

- Rely exclusively on direct operating system environment variable lookups without a configuration library. (rejected)
  Rejected because: Lacks structured file support, hierarchical key management, type coercion, and seamless command flag integration.
  When valid: Minimal single-purpose utility binaries with no flag or file configuration requirements.
- Implement bespoke file parsing alongside standard library environment lookups. (rejected)
  Rejected because: Increases maintenance overhead and leads to inconsistent configuration semantics and precedence rules across runtime binaries.
  When valid: Zero-dependency environments where external library adoptions are prohibited.

## Risks

- Uncoordinated direct environment access alongside configuration registry calls can create inconsistent setting resolution.
  Mitigation: Enforce strict architectural separation between bootstrap environment reads and application configuration retrieval via lint checks.
  Owner: engineering team
- Global configuration registry state can introduce tight coupling and race conditions during concurrent test execution.
  Mitigation: Encapsulate configuration instances or pass configured domain structs explicitly across service boundaries.
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
- Configure configuration precedence to systematically prioritize command flags over environment variables, and environment variables over configuration file defaults.
- Isolate early bootstrap lookups to dedicated initialization routines before handing control to components bound to the centralized configuration registry.

## Continuation Context

Verify commands:

- Discover and run the repository test suite to validate configuration binding and parsing behavior.
- Execute the repository code formatting and static analysis checks to ensure configuration API compliance.

Accept when:

- All unit and integration tests verifying configuration loading and flag bindings pass without failure.
- Static analysis validates that application configuration lookups use the designated configuration interface.

## Enforcement

- Verified by: Automated continuous integration test suites.
- Verified by: Static analysis rules and peer code reviews during pull request inspection.
- Violation handling: Pull requests with unauthorized direct file parsing or bypassing configuration access patterns will be blocked in code review.
- Violation handling: Violating components must be refactored to consume the centralized configuration provider or accept configuration via domain structures.
- Exception process: Submit an architecture exception request detailing why the centralized configuration subsystem cannot be used during component initialization.
- Exception process: Obtain approval from the Runtime Architecture Lead prior to merging code that introduces direct environment or file parsing.
