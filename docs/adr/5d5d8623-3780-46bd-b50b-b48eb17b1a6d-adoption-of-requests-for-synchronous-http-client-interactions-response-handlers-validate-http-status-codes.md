---
version: 2
name: 5d5d8623-3780-46bd-b50b-b48eb17b1a6d-adoption-of-requests-for-synchronous-http-client-interactions-response-handlers-validate-http-status-codes
description: "Adoption of Requests for Synchronous HTTP Client Interactions: Response Handlers Validate Http Status Codes"
metadata:
  actual:
    adr_id: 5d5d8623-3780-46bd-b50b-b48eb17b1a6d
    status: accepted
    candidate_group_id: 07dc8210-bda7-4db3-bc0f-4a896883f0bd
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Adoption of Requests for Synchronous HTTP Client Interactions: Response Handlers Validate Http Status Codes

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`
- `src/tests_common/test_utils/**`

## Context

- Integration test helpers and documentation extensions require reliable synchronous communication with local API services and external metadata registries.
- Custom HTTP wrappers and fragmented client implementations lead to inconsistent retry handling, disparate connection pooling, and duplicated session authentication logic.
- Standardizing on an established HTTP client library provides unified interfaces for session management, request adapters, and response parsing across testing and build infrastructure.

## Problem Statement

Developer utilities and integration test suites require a standardized mechanism for issuing synchronous HTTP requests, maintaining persistent authenticated sessions, and handling remote API payloads without proliferating redundant networking boilerplate.

## Decision

- **R-LM-001** MUST: Response handlers MUST validate HTTP status codes and catch requests.exceptions.RequestException subclasses to avoid unhandled network error propagation.

## Policy Block

In scope:

- Synchronous HTTP client interactions within developer utilities, test helpers, and documentation build extensions.

Out of scope:

- Asynchronous or reactive network streaming pipelines requiring dedicated event loop architectures.
- Internal inter-process communication mechanisms operating over non-HTTP transport layers.

## Rationale

- Adopting requests establishes a unified API for session persistence, authentication headers, and JSON response handling across test helpers and documentation extensions.
- Centralizing on a single established HTTP client avoids conflicting transport adapters and inconsistent exception hierarchies across developer infrastructure.
- Built-in support for transport adapters facilitates standardized retry strategies for transient network failures.

## Consequences

Positive:

- Standardized session lifecycle and connection pool reuse across testing utilities and build extensions.
- Consistent exception hierarchy for handling network interruptions and HTTP protocol errors across subsystems.
- Simplified token injection and payload formatting for REST API integration testing.

Negative:

- Synchronous network calls block the executing thread until the remote endpoint responds or times out.
- Introduces an external dependency requiring monitoring for security advisories and maintenance updates.

## Alternatives

- Standard library low-level HTTP client modules (rejected)
  Rejected because: Lacks built-in persistent session management, automatic JSON decoding, and configurable transport adapters without substantial boilerplate code.
  When valid: When external runtime dependencies are strictly prohibited and only trivial single-shot HTTP requests are required.
- Asynchronous HTTP client libraries (rejected)
  Rejected because: Introduces unnecessary event loop complexity into synchronous test runners and documentation generation extensions.
  When valid: When high-throughput non-blocking concurrency or reactive streaming is required by the operational subsystem.

## Risks

- Synchronous network calls executed without timeouts can hang automated test or build pipelines indefinitely.
  Mitigation: Configure explicit connect and read timeout parameters on all client calls to prevent process freezing.
  Owner: engineering team
- Repeated unpooled client invocations may cause socket exhaustion under heavy test suite execution.
  Mitigation: Encapsulate client operations within managed session contexts and configure connection pool limits via transport adapters.
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
- Initialize custom retry transport adapters and persistent authentication headers on shared session objects to optimize connection reuse across multi-step flows.
- Ensure explicit connect and read timeout values are specified on all synchronous network dispatches to avoid indefinite blocking during test runs.

## Continuation Context

Verify commands:

- Discover and execute the repository test runner targeting external API client and integration helper test suites.
- Execute the project static analysis and dependency validation check to verify compliance of networking client imports.

Accept when:

- All test suites exercising external API clients and documentation helpers pass with expected HTTP response handling.
- Static verification confirms that all synchronous HTTP client calls in developer and test utilities import and use the approved library without unhandled socket errors.

## Enforcement

- Verified by: Automated test execution suites verifying API client interactions.
- Verified by: Static analysis rules validating that network client imports conform to approved libraries.
- Verified by: Peer code review during pull request inspection.
- Violation handling: Pull requests introducing unauthorized HTTP client dependencies will be blocked by automated checks.
- Violation handling: Code using unapproved networking patterns must be refactored to use standard session utilities prior to merging.
- Exception process: Submit an architectural exception request detailing technical incompatibilities that prevent synchronous client adoption.
- Exception process: Obtain formal approval from infrastructure maintainers before introducing alternative HTTP transport libraries.
