---
version: 2
name: 94123943-3059-468d-8161-0fa0207585dd-cadwyn-api-versioning-framework-adoption-engineers-not-bypass-versionchange-migration-classes
description: "Cadwyn API Versioning Framework Adoption: Engineers Not Bypass Versionchange Migration Classes"
metadata:
  actual:
    adr_id: 94123943-3059-468d-8161-0fa0207585dd
    status: accepted
    candidate_group_id: f3015b06-763b-4003-8767-ca8a62bfc415
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Cadwyn API Versioning Framework Adoption: Engineers Not Bypass Versionchange Migration Classes

Date: 2025-02-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- The execution API must support distributed execution clients running across decoupled environments that update at differing cadences.
- Evolving data models for task execution context, run metadata, and system payloads risks breaking older distributed worker agents if breaking contract changes are applied without adaptation.
- The codebase adopts the cadwyn library to decouple domain evolution from protocol backwards compatibility through sequential schema mutation instructions and response downgrade decorators.

## Problem Statement

As execution API schemas evolve to support new execution parameters and workflow properties, introducing structural changes directly into shared payload models breaks backward compatibility with older workers. Without a centralized and declarative version migration mechanism, maintaining compatibility requires ad-hoc conditional branching or controller duplication, increasing maintenance overhead and risk of regression across distributed execution environments.

## Decision

- **R-LM-001** MUST_NOT: Engineers MUST_NOT bypass VersionChange migration classes by embedding version negotiation or version branching logic directly within execution endpoint handlers.

## Policy Block

In scope:

- Execution API route definitions, schema models, and response serialization contracts.
- Modifications to public API payloads that require backward compatibility across deployed worker components.

Out of scope:

- Internal executor workload data structures that do not cross remote client-server network boundaries.
- Private utility modules and non-versioned administrative interfaces.

Exceptions:

- **EXC-20-001** Urgent security patches where strict backward compatibility must be severed to prevent vulnerability exploitation.
  Approval: API Architecture Review Board
  Documentation: Document the breaking change in the security advisory and release notices.

## Rationale

- Cadwyn allows the current domain model to serve as the single source of truth while defining past version representations as declarative diffs.
- Encapsulating version transformations into discrete VersionChange classes isolates migration logic from standard business logic in route handlers.
- Declarative migration instructions reduce boilerplate and provide structured metadata for automated request and response schema transformation.

## Consequences

Positive:

- Route handlers and domain models remain focused on the latest schema contracts without scattering legacy compatibility checks throughout application code.
- Decoupled distributed workers communicate reliably with the control plane across differing version states without requiring lockstep cluster upgrades.
- Version migrations are modular, auditable, and removable when historical versions reach end-of-life.

Negative:

- Chaining multiple sequential version transitions introduces transformation processing overhead for legacy clients.
- Developers must write and validate migration instructions and response converter functions for every contract modification.

## Alternatives

- Manual per-endpoint version routing and serialization branching (rejected)
  Rejected because: Embedding conditional branching for past schema formats pollutes core execution logic with legacy translation concerns.
  When valid: When an API maintains only a single active client release without backward-compatibility requirements.
- URI-based schema duplication with separate controller classes per version (rejected)
  Rejected because: Controller duplication creates widespread code drift and redundant bug-fixing efforts across historical API branches.
  When valid: When successive versions represent completely divergent protocol paradigms rather than incremental field adjustments.

## Risks

- Accumulation of extensive chained migration transformations degrades response serialization latency for legacy clients.
  Mitigation: Establish a scheduled deprecation and sunset policy for historical versions to prune obsolete migration chains.
  Owner: API Platform Engineering
- Response body field pruning in downgrade handlers might inadvertently discard fields required by legacy consumers if schema migration tuples diverge from runtime contracts.
  Mitigation: Enforce contract verification tests comparing serialized downgrade payloads against baseline schema definitions for each supported version.
  Owner: API Platform Engineering

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Create an isolated migration definition subclassing VersionChange for each temporal contract change, declaring inverse transformations to migrate requests and responses toward older schemas.
- Implement response body mutations using convert_response_to_previous_version_for when property types or nested dictionary formats differ between successive schema revisions.

## Continuation Context

Verify commands:

- Discover the test runner invocation from the project manifest and execute the test suite for API version change schemas.
- Discover the static analysis scripts from repository configuration and run verification across all migration modules.
- Discover the contract verification suite to validate backward-compatibility transformations against previous client schemas.

Accept when:

- All declared VersionChange classes transform models consistently between the latest schema and designated previous version contracts.
- Verification suites confirm response downgrade functions generate expected payload structures without runtime transformation errors.
- Static verification confirms that all modified public API routes maintain corresponding migration instructions.

## Enforcement

- Verified by: Automated contract test suites verifying schema conversions across each declared version.
- Verified by: Architecture review during pull requests affecting execution API models and endpoints.
- Violation handling: Pull requests altering public schemas without corresponding VersionChange declarations are blocked during integration verification.
- Exception process: Submit an architectural review request with justification detailing why a hard breaking change is necessary, requiring approval from the API architecture leads.
