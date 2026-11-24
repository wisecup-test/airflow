---
version: 2
name: 642e8e1f-21e2-4353-b9c2-05cbeab73782-vitest-configuration-via-vitest-config-for-unit-testing-unit-test-configurations-not-embed-environment
description: "Vitest Configuration via vitest/config for Unit Testing: Unit Test Configurations Not Embed Environment"
metadata:
  actual:
    adr_id: 642e8e1f-21e2-4353-b9c2-05cbeab73782
    status: accepted
    candidate_group_id: 03e7f149-5ff1-4a59-9734-db3e688743cb
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - react-plugin-tools/react_plugin_template/**
---

# Vitest Configuration via vitest/config for Unit Testing: Unit Test Configurations Not Embed Environment

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`
- `react-plugin-tools/react_plugin_template/**`

## Context

- Unit test execution and runner configurations are defined within isolated template workspaces to validate plugin modules independently.
- The codebase maintains multiple language environments where test runners and test path orchestration operate under divergent tooling configurations.
- Configuration for component unit testing in plugin packages uses defineConfig from vitest/config to declare testing behavior within the workspace build configuration.

## Problem Statement

Inconsistent test runner definitions across isolated workspaces risk configuration drift and unstandardized test execution contracts. A formal standard is required to govern how unit testing configuration is declared using vitest/config within plugin workspaces while maintaining clear boundaries from external runner scripts.

## Decision

- **R-TS-001** MUST_NOT: Unit test configurations MUST NOT embed environment-specific orchestration scripts or execution paths intended for broader repository management boundaries.

## Policy Block

In scope:

- Plugin template workspaces that declare unit testing configurations using vitest/config

Out of scope:

- Repository-level integration test runners and deployment validation scripts

## Rationale

- Adopting defineConfig from vitest/config standardizes the configuration structure for unit testing in plugin templates.
- Encapsulating test runner parameters within workspace-level configuration prevents configuration drift across distinct plugin implementations.
- Differentiating unit testing setup from developer environment dispatch scripts prevents coupling local component verification to repository-level tooling.

## Consequences

Positive:

- Standardizes unit test configuration across plugin template workspaces.
- Isolates unit test runner configuration from environment-level test execution scripts.
- Ensures consistent type-safe test runner definitions via defineConfig.

Negative:

- Requires maintaining separate testing configurations across distinct language and project boundaries.
- Increases configuration surface area when maintaining templates alongside repository-wide tooling.

## Alternatives

- Adopting repository-wide monolithic test runner scripts for all workspaces (rejected)
  Rejected because: Couples independent plugin packages to global developer tooling and complicates isolated module development.
  When valid: Valid when all workspace components share an identical runtime environment and deployment lifecycle.
- Unconfigured test execution relying entirely on runner defaults (rejected)
  Rejected because: Leaves plugin unit test behavior unstandardized and vulnerable to divergent execution environments.
  When valid: Valid for trivial single-file scripts with no external plugin build requirements.

## Risks

- Configuration divergence between workspace unit test runners and repository continuous integration pipelines.
  Mitigation: Validate workspace test configurations against centralized continuous integration checks before release.
  Owner: engineering team
- API incompatibilities when updating versioned configuration dependencies.
  Mitigation: Adhere to the lock-version grounding policy by inspecting lock artifacts and documentation before updating configurations.
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
- Inspect the workspace configuration file to ensure defineConfig is imported directly from the designated testing configuration module.
- Ensure test runner parameters are scoped strictly to the unit test suite within the local workspace.

## Continuation Context

Verify commands:

- Discover the workspace test execution script from the project manifest and execute it to verify unit test configuration.
- Discover the project build verification command from the workspace configuration and execute it to confirm build and type validation succeed.

Accept when:

- The workspace test runner executes all unit tests successfully according to the configuration definition.
- The build and configuration validation processes complete with zero exit codes and no configuration syntax errors.

## Enforcement

- Verified by: Automated continuous integration checks that execute workspace test suites
- Verified by: Peer code review of configuration files in template workspaces
- Violation handling: Continuous integration job failures blocking pull request merges
- Violation handling: Code review rejection requiring correction to standard test configuration definitions
- Exception process: Submit an architectural exception request documented with rationale to the engineering lead for review
