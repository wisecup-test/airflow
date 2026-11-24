---
version: 2
name: 8955c22b-f44c-46f7-bf49-aa41e433d56c-adoption-of-airflowctl-api-client-for-cli-command-remote-operations-command-handlers-not-instantiate-bespoke-http
description: "Adoption of airflowctl.api.client for CLI Command Remote Operations: Command Handlers Not Instantiate Bespoke Http"
metadata:
  actual:
    adr_id: 8955c22b-f44c-46f7-bf49-aa41e433d56c
    status: accepted
    candidate_group_id: 1a59d1a5-7c69-4cc2-8f66-a37d159163e9
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/ctl/commands/**
---

# Adoption of airflowctl.api.client for CLI Command Remote Operations: Command Handlers Not Instantiate Bespoke Http

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `src/airflowctl/ctl/commands/**`

## Context

- CLI subcommands require a uniform mechanism to deserialize user inputs and dispatch operational tasks against remote services.
- Multiple command handlers coordinate operational actions such as configuration management, import operations, and bulk updates through dedicated client boundaries.
- Standardizing command interaction on a shared API client module prevents fragmentation of network transport logic across command handlers.

## Problem Statement

Command modules require a consistent, maintainable approach for dispatching administrative operations without duplicating network transport, authentication, and endpoint contracts across individual command implementations.

## Decision

- **R-LM-001** MUST_NOT: Command handlers MUST NOT instantiate bespoke HTTP or network transport sessions outside the designated API client boundary.

## Policy Block

In scope:

- All CLI command handlers executing remote operations or managing configuration resources.
- Command handlers performing import, export, or synchronization workflows with backend services.

Out of scope:

- Purely local terminal utilities that require no communication with remote services.
- Internal client library implementation code itself.

## Rationale

- Analysis across command implementations shows shared dependency on airflowctl.api.client and generated data contracts for administrative actions.
- Centralizing remote interaction logic within the dedicated client module minimizes repetitive network error handling across CLI entry points.
- Decoupling CLI command presentation from underlying API transport enables independent evolution of presentation and network protocols.

## Consequences

Positive:

- Decouples command parsing, input loading, and terminal formatting from transport protocol details.
- Enforces a consistent boundary for API interactions, error formatting, and input serialization across all CLI commands.
- Facilitates automated testing by isolating network I/O behind an explicit client contract.

Negative:

- Subcommand features depend strictly on the feature parity and method availability of the internal API client module.
- Updates to backend contracts necessitate coordinated updates to the shared client and datamodel interfaces.

## Alternatives

- Direct HTTP client implementation within individual CLI command modules (rejected)
  Rejected because: Causes duplication of authentication, connection handling, and endpoint routing logic across distinct subcommands.
  When valid: Valid only for standalone one-off utility scripts operating outside the main command hierarchy.
- Direct database access bypassing the service API layer (rejected)
  Rejected because: Couples CLI tooling directly to the backing persistence schema, bypassing server-side validation and access controls.
  When valid: Valid only for offline migration scripts or low-level diagnostic utilities requiring raw database connectivity.

## Risks

- Breaking changes in client methods affect all dependent command modules simultaneously.
  Mitigation: Maintain strict semantic versioning and regression test coverage over client contract methods.
  Owner: engineering team
- Inconsistent error handling of client communication failures across different commands.
  Mitigation: Encapsulate transport error conversion within standardized command utility handlers.
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
- Command modules should isolate filesystem reading and deserialization prior to dispatching payloads through client methods.
- Client invocation failures must be captured and translated into standardized user-facing terminal output.

## Continuation Context

Verify commands:

- Discover and execute the repository static analysis and linting suite to verify compliance with boundary rules across command modules.
- Discover and run the project automated test suite to confirm command integration with client interfaces.

Accept when:

- Command implementations execute remote mutations exclusively through the designated API client interface.
- Local serialization and deserialization routines map directly to defined contract models without ad-hoc network requests.
- Project verification and static analysis suites report zero violations across command handlers.

## Enforcement

- Verified by: Automated continuous integration checks inspecting imports and call boundaries across command modules.
- Verified by: Peer code review verifying adherence to the shared client module for service interactions.
- Violation handling: Code modifications introducing unapproved direct network calls or alternative client implementations are rejected during review.
- Violation handling: Non-compliant commands must be refactored to consume the centralized client interface before merging.
- Exception process: Requests for exceptions must be submitted via architectural review with justification for bypassing standard client boundaries.
- Exception process: Approved exceptions require formal tracking and an identified roadmap for eventual alignment with the API client.
