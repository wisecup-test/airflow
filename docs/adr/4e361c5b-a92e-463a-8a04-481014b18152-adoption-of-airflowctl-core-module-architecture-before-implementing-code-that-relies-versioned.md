---
version: 2
name: 4e361c5b-a92e-463a-8a04-481014b18152-adoption-of-airflowctl-core-module-architecture-before-implementing-code-that-relies-versioned
description: "Adoption of airflowctl Core Module Architecture: Before Implementing Code That Relies Versioned"
metadata:
  actual:
    adr_id: 4e361c5b-a92e-463a-8a04-481014b18152
    status: accepted
    candidate_group_id: 42c052e8-12a7-48e1-b50d-1f4a4dc19b96
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/ctl/commands/**
---

# Adoption of airflowctl Core Module Architecture: Before Implementing Code That Relies Versioned

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `docs/**`
- `src/airflowctl/ctl/commands/**`

## Context

- Internal domain operations and administrative commands require shared access to central service client contracts and core abstractions.
- Documentation builds and command-line execution interfaces require consistent references to project package metadata and runtime models.
- A modular package boundary allows clean decoupling between presentation layers and underlying communication clients.

## Problem Statement

Disparate command interfaces and documentation tooling require uniform access to internal package structures and client interaction logic without introducing redundant implementations.

## Decision

- **R-LM-001** MUST: Before implementing code that relies on versioned dependencies, the consumer MUST discover the ecosystem lock artifact and verify the exact resolved version.

## Policy Block

In scope:

- All command modules, client consumers, and documentation configuration scripts interacting with internal package utilities.

Out of scope:

- Third-party utility libraries operating independently of internal application state.

## Rationale

- Centralizing core abstractions in a unified internal module prevents logic duplication across presentation and documentation layers.
- Structured client submodules isolate communication protocols from administrative commands.

## Consequences

Positive:

- Centralizes shared domain interfaces and client representations across documentation, tooling, and command surfaces.
- Encapsulates downstream API payloads into structured internal contract models.

Negative:

- Requires disciplined namespace management to prevent circular module dependencies as subpackages expand.
- Changes to foundational schemas in the primary package require coordinated updates across consumer surfaces.

## Alternatives

- Direct inline script logic without an intermediate shared package boundary (rejected)
  Rejected because: Violates modular separation and duplicates domain models across interface endpoints and ancillary tooling.
  When valid: Appropriate only for standalone throwaway scripts with zero shared domain logic.
- Third-party client dependency direct invocation without a project namespace wrapper (rejected)
  Rejected because: Couples internal application commands directly to external schema structures without central adaptation.
  When valid: Valid when consuming external endpoints with no specialized internal client abstractions.

## Risks

- Tight coupling between client data models and consumer presentation logic could impede protocol updates.
  Mitigation: Maintain strict modular boundaries and automated dependency analysis to detect illegal cross-subpackage imports.
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
- Structure all consumer modules to import shared capabilities strictly from the top-level package namespace hierarchy.
- Ensure client contract serializations derive systematically from verified data models prior to consumer presentation.

## Continuation Context

Verify commands:

- Discover and execute the repository linter and import validation target to ensure boundary integrity.
- Discover and execute the repository test suite to verify internal package contract compatibility.

Accept when:

- All modules consuming the internal package resolve interfaces through the designated namespace hierarchy without circular imports.
- Automated test suites and build checks execute successfully across all entry points invoking the core module.

## Enforcement

- Verified by: Automated static import analysis, packaging lint checks, and unit test suites during continuous integration.
- Violation handling: Build failure upon detection of undeclared module imports or direct bypass of established package boundaries.
- Exception process: Submit an architectural change proposal documenting interface constraints and import necessity to the engineering leads.
