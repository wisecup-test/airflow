---
version: 2
name: 8ea65b2f-d499-43d7-9bc1-3a171769a060-adopt-cadwyn-for-api-versioning-and-schema-migrations-developers-write-active-endpoint-logic-against
description: "Adopt Cadwyn for API Versioning and Schema Migrations: Developers Write Active Endpoint Logic Against"
metadata:
  actual:
    adr_id: 8ea65b2f-d499-43d7-9bc1-3a171769a060
    status: accepted
    candidate_group_id: 7d1d5e64-ead0-46c0-97ec-af4694a11090
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/execution_api/**
---

# Adopt Cadwyn for API Versioning and Schema Migrations: Developers Write Active Endpoint Logic Against

Date: 2025-02-17
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/api_fastapi/execution_api/**`

## Context

- The execution API must support evolving capabilities while preserving backward compatibility for distributed workers and clients on older releases.
- Manual version branching inside route handlers creates high maintenance friction and couples core endpoint logic to obsolete data representations.
- Cadwyn provides a structured versioning mechanism that decouples current internal schemas from legacy payload formats through version change migrations.

## Problem Statement

Supporting diverse client versions against an evolving execution API without code duplication or messy in-handler version checks requires an automated, declarative mechanism to translate request and response schemas backward.

## Decision

- **R-LM-001** MUST: Developers MUST write active endpoint logic against the latest canonical data models without embedding conditional version branching inside standard route execution paths.

## Policy Block

In scope:

- All execution API route definitions, schemas, and version migration modules.
- Modifications to client-facing HTTP payloads and endpoint availability across API releases.

Out of scope:

- Internal data access layers and database migrations.
- Non-versioned administrative or private utility endpoints.

## Rationale

- Cadwyn centralizes API versioning by defining version diffs as modular migration units, keeping route handlers focused on the latest schema.
- Declarative migration directives and response conversion decorators ensure verifiable and consistent backward compatibility across releases.

## Consequences

Positive:

- Maintains a single canonical implementation of business logic and route handlers against the newest data models.
- Automates backward compatibility schema translations for legacy clients without route handler duplication.
- Provides explicit, traceable records of schema and endpoint evolution over chronological releases.

Negative:

- Requires developers to understand Cadwyn declarative transformation syntax and response converter semantics.
- Accumulates a growing chain of historical migrations that must be executed and tested on each older client request.

## Alternatives

- URL path prefix versioning with duplicate router trees (rejected)
  Rejected because: Duplicating router trees and handler functions for every release causes code bloat, high maintenance overhead, and bug drift across version branches.
  When valid: Valid when distinct API versions represent fundamental architecture rewrites with no shared operational logic.
- Manual conditional branching inside endpoint handlers (rejected)
  Rejected because: Scattering version checks inside business logic clutters route handlers and couples route implementation to client compatibility details.
  When valid: Valid in minimal single-endpoint services where full migration pipelines introduce disproportionate complexity.

## Risks

- Complex chained transformations across multiple historical versions may introduce subtle payload translation errors.
  Mitigation: Ensure comprehensive automated test coverage for payload transformations across every registered historical version.
  Owner: engineering team
- Runtime translation overhead for deeply nested schemas when serving very old client versions.
  Mitigation: Profile serialization overhead and establish retirement policies for long-deprecated API releases.
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
- Organize version change definitions chronologically in designated version modules, capturing all endpoint and schema alterations.
- Implement tests verifying both contemporary responses and downgraded payloads across each registered version transition.

## Continuation Context

Verify commands:

- Discover and execute the project API test suite targeting execution API version migration pathways.
- Run the project contract verification and schema validation procedures to confirm version migration integrity across all declared releases.

Accept when:

- All automated test suites covering API version migrations and backward-compatibility payload transformations pass without error.
- All registered version changes successfully transform contemporary schemas and response payloads into legacy formats across every historical API release.

## Enforcement

- Verified by: Automated test suites validating backward compatibility conversions against prior version schemas.
- Verified by: Pull request code reviews ensuring new or modified endpoints define corresponding version migration classes.
- Violation handling: Pull requests introducing schema changes or endpoint modifications without required migration definitions are blocked in continuous integration.
- Violation handling: Code review rejection for route handlers containing ad-hoc manual version conditional checks.
- Exception process: Exceptions to version migration structures require review and formal approval from the API architecture maintainers.
- Exception process: Any bypass of backward compatibility guarantees must be documented alongside an explicit client deprecation notice.
