---
version: 2
name: 85b0edfc-af97-4d8e-a186-fb2caf2bea0d-internal-api-client-architecture-with-airflowctl-api-client-remote-service-error-handling-standardized-through
description: "Internal API Client Architecture with airflowctl.api.client: Remote Service Error Handling Standardized Through"
metadata:
  actual:
    adr_id: 85b0edfc-af97-4d8e-a186-fb2caf2bea0d
    status: accepted
    candidate_group_id: 2494e877-27cf-4197-bce4-f4df5012e098
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# Internal API Client Architecture with airflowctl.api.client: Remote Service Error Handling Standardized Through

Date: 2025-02-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `src/airflowctl/**`

## Context

- Command-line interface commands require access to backend service endpoints for managing pools, connections, variables, authentication, and asset metadata.
- Issuing direct HTTP requests from individual command modules introduces divergent error handling, inconsistent retry strategies, and duplicate authentication mechanics.
- The codebase establishes a unified internal client boundary in airflowctl.api.client that encapsulates transport configuration, token propagation, retry policies, and structured exception conversion.

## Problem Statement

Without a standardized internal API client layer, command implementations would directly manage network connections, authentication tokens, retry backoffs, and HTTP response decoding. This causes duplicated transport boilerplate, inconsistent error messaging to users, and fragile couplings between presentation code and remote endpoint contracts.

## Decision

- **R-IA-001** MUST: Remote service error handling MUST be standardized through ServerResponseError parsing and typed connection exceptions rather than raw HTTP transport status inspection in individual command modules.

## Policy Block

In scope:

- All command-line interface handlers interacting with internal REST services.
- All internal client operations and service adapter modules.

Out of scope:

- Local utility scripts operating entirely on offline filesystem artifacts without network transport.
- Console formatting and presentation renderers processing pre-fetched data structures.

## Rationale

- Centralizing service communication in airflowctl.api.client ensures uniform retry behavior, structured error reporting via ServerResponseError, and centralized authentication handling.
- Decoupling command dispatch logic from underlying HTTP clients enables independent evolution of transport infrastructure, logging, and security headers without modifying command handlers.
- Strongly-typed operation wrappers provide clean boundaries and predictable data schemas for downstream console renderers.

## Consequences

Positive:

- Standardized retry resilience and credential resolution across all command-line interface subcommands.
- Uniform error parsing and exception hierarchy preventing unhandled network tracebacks from reaching end users.
- Clear architectural separation between terminal interaction logic and remote service protocols.

Negative:

- Additional layer of indirection requiring new backend capabilities to be exposed through client operations before command consumption.
- Need to maintain synchronization between internal API client data models and remote service schemas.

## Alternatives

- Direct inline HTTP requests from command modules using arbitrary network libraries (rejected)
  Rejected because: Scatters authentication logic, retry management, error serialization, and connection lifecycle across individual commands, leading to inconsistent error behavior and maintenance overhead.
  When valid: Prototyping isolated single-use command scripts with zero dependency on shared authentication or retry conventions.
- Auto-generated external client SDK without custom internal wrappers (rejected)
  Rejected because: Lacks command-line specific context handling such as terminal error suppression, unified credential resolution, and domain-specific exception translation.
  When valid: Public external consumer integrations where generic API contracts suffice without command-line presentation or retry constraints.

## Risks

- Central client abstraction layer may become a bottleneck if changes to backend endpoints require coordinated updates across client models and operations.
  Mitigation: Maintain clear separation between general transport mechanics and domain-specific endpoint operations, using structured data models for automated schema alignment.
  Owner: engineering team
- Aggressive or misconfigured exponential retry logic at the client boundary could amplify backend load during server outages.
  Mitigation: Enforce strict bounded retry limits and jittered exponential backoff with exception filtering in client request decorators.
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
- Ensure all newly introduced service endpoints define corresponding methods within the operations layer that consume the shared client instance.
- Configure authentication credentials and environment context exclusively through client initialization utilities rather than passing security credentials directly into command execution functions.

## Continuation Context

Verify commands:

- discover_and_run_lint_checks
- discover_and_run_type_checks
- discover_and_run_unit_tests

Accept when:

- All command modules import internal service functionality exclusively from the designated client module without unmediated network calls.
- Static type analysis and linters confirm zero direct network library instantiations outside the client boundary.
- Test suite passes with all client operations asserting proper retry configuration and error translation.

## Enforcement

- Verified by: Automated continuous integration pipeline running static analysis and import boundary checks.
- Verified by: Peer code review verifying that new commands consume backend services solely through the client operations module.
- Violation handling: Pull requests introducing direct HTTP requests outside the designated client module fail automated linting and boundary validation checks.
- Violation handling: Violating implementations must be refactored to register operations within the client boundary before merge approval.
- Exception process: Architectural review submission justifying why the existing client boundary cannot accommodate the requirement, accompanied by an approved transition plan.
