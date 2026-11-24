---
version: 2
name: 9e529925-e552-402b-ad62-ab6c09090949-adoption-of-tests-common-test-utils-version-compat-for-shared-test-compatibility-abstraction-consumers-inspect-repository-dependency-ce06eeba207c16ed
description: "Adoption of tests_common.test_utils.version_compat for Shared Test Compatibility Abstraction: Consumers Inspect Repository Dependency Resolution Artifact"
metadata:
  actual:
    adr_id: 9e529925-e552-402b-ad62-ab6c09090949
    status: accepted
    candidate_group_id: 0994bdd3-ef77-4230-9922-fbecab2a94e6
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Adoption of tests_common.test_utils.version_compat for Shared Test Compatibility Abstraction: Consumers Inspect Repository Dependency Resolution Artifact

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/tests_common/test_utils/**`

## Context

- Shared testing utilities across the repository require consistent execution across multiple runtime environments and evolving module boundaries.
- Disparate test helpers previously handled structural changes in plugin managers, operator links, and message queues via localized conditional logic.
- Consolidating version-dependent behaviors and deprecation tracking into tests_common.test_utils.version_compat establishes a single source of truth for cross-version test execution.

## Problem Statement

When framework subsystems evolve across revisions, test utilities such as mock operators, message queue fixtures, and plugin managers encounter breaking changes in module locations, attribute schemas, and parameter deprecations. Ad-hoc branching across disparate test files creates maintenance overhead and test flakiness, necessitating a unified compatibility abstraction layer.

## Decision

- **R-LM-001** MUST: Consumers MUST inspect the repository dependency resolution artifact to determine the authoritative locked version of all relevant runtime modules before implementing compatibility adapters.

## Policy Block

In scope:

- Shared testing harnesses, mock operators, test doubles, and test fixtures defined in common test utilities.
- Test components that handle deprecation warnings or version-divergent architectural subsystems.

Out of scope:

- Production runtime code outside the shared testing utility namespace.
- Tests executing against invariant module interfaces that require no version branching.

Exceptions:

- **EXC-20-001** A test explicitly validates the failure mode of an unsupported or malformed runtime environment.
  Approval: Architecture Review Board
  Documentation: Document in test header comments explaining why the centralized compatibility module is bypassed.

## Rationale

- Evidence demonstrates consistent reliance on tests_common.test_utils.version_compat across mock operators, message queue deprecation fixtures, and plugin manager doubles.
- Centralizing compatibility checks isolates breaking structural changes such as plugin manager namespace transitions and trigger parameter deprecations into a single maintainable boundary.
- Providing uniform test doubles and warning interceptors reduces boilerplate in consumer test suites.

## Consequences

Positive:

- Provides a single maintenance locus for adapting test harnesses to runtime and subsystem evolutions.
- Minimizes test suite flakiness resulting from disparate version detection logic across test utilities.
- Simplifies deprecation verification and test double definitions across common test packages.

Negative:

- Introduces an internal abstraction dependency that must be maintained whenever underlying subsystem contracts change.
- Requires test utility authors to maintain forward and backward compatibility mappings simultaneously within the shared module.

## Alternatives

- Inline conditional branching within individual test helper modules (rejected)
  Rejected because: Scattering conditional imports and attribute checks across multiple modules causes configuration drift, duplicated logic, and high maintenance overhead.
  When valid: When a version difference is entirely isolated to a single standalone test file with no cross-suite reuse.
- Separate test suites and fixtures per target runtime environment (rejected)
  Rejected because: Maintaining duplicated parallel test suites significantly increases code duplication and test execution duration.
  When valid: When major architectural changes render test structures completely incompatible between environments.

## Risks

- Compatibility abstraction layer may mask unintended behavioral divergences between runtime environments.
  Mitigation: Ensure matrix testing runs test suites against all supported dependency combinations with strict warning escalation.
  Owner: engineering team
- Accumulation of obsolete compatibility shims after older runtime revisions reach end of life.
  Mitigation: Establish periodic audits to prune legacy branches once minimum supported runtime baselines advance.
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
- Define all compatibility-sensitive mock operators and plugin doubles by routing structural inspections through the shared compatibility module.
- When testing deprecation warnings, assert against standard deprecation warning classes provided through the compatibility hierarchy.

## Continuation Context

Verify commands:

- Discover the project test execution runner and run the test suite targeting shared testing utilities.
- Discover the repository static analysis and type checking utilities to verify compatibility interface contracts.

Accept when:

- All shared test utilities and fixtures execute without errors across all configured environment matrix variants.
- No direct conditional version branching exists outside the centralized compatibility module.

## Enforcement

- Verified by: Automated continuous integration matrix runs validating test execution across supported target environments.
- Verified by: Peer code review ensuring new test helpers import compatibility shims rather than implementing inline checks.
- Violation handling: Pull requests introducing direct inline version branching in test helpers will be blocked during code review.
- Exception process: Exceptions must be submitted via architectural review request and documented with rationale in test module headers.
