---
version: 2
name: e1ce783a-2224-4663-8feb-ff10af230648-openapi-requests-types-gen-internal-contract-module-adoption-complex-domain-transformations-display-aggregations-compose
description: "openapi/requests/types.gen Internal Contract Module Adoption: Complex Domain Transformations Display Aggregations Compose"
metadata:
  actual:
    adr_id: e1ce783a-2224-4663-8feb-ff10af230648
    status: accepted
    candidate_group_id: a801354c-7903-4feb-92e8-39ee937f33e4
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# openapi/requests/types.gen Internal Contract Module Adoption: Complex Domain Transformations Display Aggregations Compose

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Client user interface components, modals, and data visualizers interact extensively with backend service endpoints that manage complex orchestration and execution state.
- Manual maintenance of client-side data transfer models and response interfaces introduces contract drift, silent runtime type discrepancies, and operational overhead as backend endpoints evolve.
- A single source of truth for request parameters, response structures, and domain entities is established via centralized contract generation from the service specification into the openapi/requests/types.gen module.

## Problem Statement

Client components and utilities require reliable, synchronized data typing when interacting with backend service APIs. Manually duplicated type declarations across UI dialogs, graph components, and utility modules lead to subtle typing mismatches, runtime contract drift, and increased regression risk during backend interface refactoring. The system requires an authoritative contract definition mechanism to enforce end-to-end schema consistency across all consuming client modules.

## Decision

- **R-LM-001** SHOULD: Complex domain transformations and display aggregations SHOULD compose or extend the imported contract types from openapi/requests/types.gen rather than redefining structural properties.

## Policy Block

In scope:

- Client presentation components, modal dialogs, status badges, and graphing utilities that consume or send backend API data
- Domain type definitions and client-side data transformation functions operating on service entities

Out of scope:

- Internal visual styling state and transient component layout flags independent of backend schemas
- Third-party utility libraries operating exclusively on generic primitive collections

## Rationale

- Centralizing data contracts in openapi/requests/types.gen ensures compile-time detection of breaking API changes and prevents schema drift between client views and backend service interfaces.
- Importing shared types across 33 UI components and utilities eliminates redundant interface declarations and unifies type definitions across varied application domains.
- Schema-driven type generation reduces manual documentation overhead and accelerates client feature delivery while maintaining strict end-to-end typing guarantees.

## Consequences

Positive:

- Eliminates type drift and contract divergence between backend service endpoints and frontend presentation components.
- Provides automated compile-time validation for API payload refactoring and deprecation across all consuming UI views.
- Unifies domain vocabulary and data entity structures across disparate client modules, dialogs, and graphing utilities.

Negative:

- Requires synchronization with backend schema generation whenever API endpoints or response payloads are altered.
- Increases dependency on contract regeneration workflows before frontend component modifications can reflect new backend capabilities.

## Alternatives

- Manual Client Interface Declarations (rejected)
  Rejected because: Manually authoring and maintaining client-side type interfaces alongside backend schemas inevitably leads to contract drift, unannounced field mismatches, and runtime failures.
  When valid: Small standalone client applications with static, unversioned endpoints that do not undergo active schema evolution.
- Shared Monorepo Cross-Language Code Sharing (rejected)
  Rejected because: Directly sharing source model definitions across distinct programming languages introduces complex build-chain coupling and cross-runtime compilation hurdles.
  When valid: Homogeneous repositories where backend and frontend runtimes share an identical programming language and compiler.

## Risks

- Out-of-sync generated contract definitions causing local build failures or masking backend contract changes.
  Mitigation: Enforce automated generation and contract validation checks within the continuous integration pipeline before build execution.
  Owner: Frontend Engineering Team
- Component bloat or circular dependency issues if types are tightly coupled across deep presentation trees.
  Mitigation: Import specific named types at the boundary of consuming components and compose domain types through clear interface extension.
  Owner: Frontend Architecture Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Configure client components to import only necessary schema types by named import from the generated contract module.
- Incorporate schema validation and type-checking passes into local developer verification workflows to catch contract mismatches early.

## Continuation Context

Verify commands:

- echo 'Discover the project manifest and execute the designated type-checking script to verify contract conformance across all client modules.'
- echo 'Discover and run the project build validation command to verify that all imports from the generated contract module resolve correctly.'

Accept when:

- All client components importing from openapi/requests/types.gen compile without static type errors.
- The project build and type-checking scripts exit with return code zero during automated verification.

## Enforcement

- Verified by: Continuous integration type-checking and automated build verification passes.
- Verified by: Peer review checks ensuring new or modified UI components import contract types exclusively from openapi/requests/types.gen.
- Violation handling: Automated pull request checks fail when type inconsistencies or broken contract references are detected.
- Violation handling: Code reviews block merge of pull requests that introduce duplicate manual interface declarations for API entities.
- Exception process: Exceptions require architectural review and approval documented in an issue tracker with a planned migration timeline.
