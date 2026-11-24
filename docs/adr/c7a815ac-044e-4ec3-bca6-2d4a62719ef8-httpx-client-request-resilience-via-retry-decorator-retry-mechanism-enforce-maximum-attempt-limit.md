---
version: 2
name: c7a815ac-044e-4ec3-bca6-2d4a62719ef8-httpx-client-request-resilience-via-retry-decorator-retry-mechanism-enforce-maximum-attempt-limit
description: "httpx.Client Request Resilience via @retry Decorator: Retry Mechanism Enforce Maximum Attempt Limit"
metadata:
  actual:
    adr_id: c7a815ac-044e-4ec3-bca6-2d4a62719ef8
    status: accepted
    candidate_group_id: 2153aeca-29a1-40ed-8044-23e6130a1e1a
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/api/**
---

# httpx.Client Request Resilience via @retry Decorator: Retry Mechanism Enforce Maximum Attempt Limit

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `src/airflowctl/api/**`

## Context

- Outbound HTTP interactions with backend services encounter transient network disruptions and temporary service unavailability
- Centralizing client communication within a dedicated client boundary requires consistent retry behavior across all request operations without scattering retry loops across calling modules
- The client request dispatcher adapts httpx.Client.request by applying the @retry decorator with exponential backoff and attempt boundaries
- Automated header handling ensures that requests with payload content default to a standard JSON content-type when not explicitly provided by the caller

## Problem Statement

Direct invocation of outbound HTTP request methods without automated retry handling exposes client operations to transient network errors and intermittent server failures. Dispersing retry logic and header normalization across diverse caller functions creates inconsistent fault-tolerance behaviors, bloats service interactions, and risks overwhelming backend endpoints when callers retry without exponential backoff and jitter.

## Decision

- **R-ISP-001** MUST: The retry mechanism MUST enforce a maximum attempt limit via stop_after_attempt and MUST specify reraise=True to surface the terminal exception when the limit is reached.

## Policy Block

In scope:

- Outbound HTTP API requests managed by the central client wrapper class
- Network transport calls targeting services subject to transient transport failures and rate limits

Out of scope:

- Non-idempotent HTTP operations that risk duplicate state mutations unless verified safe by a specialized predicate
- Local in-memory protocol calls and static file system operations

Exceptions:

- **EX-24-001** Requests require non-standard backoff intervals or instant failure semantics without retries
  Approval: Lead Architect
  Documentation: Document custom retry predicate and justification at the client invocation site

## Rationale

- Centralizing resilience logic within the request dispatch method of httpx.Client avoids boilerplate duplication across individual endpoint calling methods
- Combining wait_random_exponential with stop_after_attempt mitigates thundering herd contention against downstream services by staggering subsequent retries
- Utilizing before_log with standard warning logging provides operational visibility into network degradation before total request failure occurs
- Decorating the base request invocation guarantees that all derived verb-specific calls inherit resilience policies consistently

## Consequences

Positive:

- Standardized fault tolerance and retry behavior across all outbound HTTP communication paths
- Mitigation of thundering herd load on backend endpoints via exponential backoff with randomized jitter
- Centralized observability into network instability via structured warning logging prior to retry sleep cycles
- Prevention of silent failures through explicit terminal exception reraise propagation

Negative:

- Increased overall latency for failing requests when remote endpoints experience prolonged service outages
- Risk of thread pool starvation if multiple concurrent requests exhaust backoff wait intervals simultaneously
- Requires operational tuning of backoff jitter intervals to align with downstream rate limit policies

## Alternatives

- Manual per-call retry loops implemented at individual invocation sites (rejected)
  Rejected because: Duplicates error-handling logic across service callers and risks inconsistent backoff configuration
  When valid: When individual endpoints require distinct non-standard backoff logic that cannot be expressed via a shared predicate
- Transport socket level retries within the low-level connection pool (rejected)
  Rejected because: Bypasses application-layer response inspection and payload validation needed to determine retry eligibility
  When valid: When retries must operate strictly on connection-establishment failures without inspecting HTTP status codes or payloads

## Risks

- Retrying mutating operations risks duplicate state transitions if the server processed the initial request prior to a network disconnect
  Mitigation: Restrict retry eligibility in the retry_if_exception predicate to safe or idempotent operations and transient network disconnects
  Owner: Engineering Team
- Excessive wait intervals during remote downtime block execution threads and degrade responsiveness
  Mitigation: Bound wait_random_exponential with strict maximum caps and configure small fixed bounds for stop_after_attempt
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Decorate the centralized request dispatch method using @retry, delegating execution to super().request to ensure all HTTP verb methods inherit resilience policies
- Derive retry configuration bounds such as attempt limits and backoff durations from runtime configuration sources

## Continuation Context

Verify commands:

- discover test runner in project configuration and run unit tests validating client retry backoff
- discover test suite execution commands and execute tests verifying exception filtering and header defaults

Accept when:

- Client request invocations retry upon encountering eligible transient exceptions up to the configured attempt threshold
- Requests fail and reraise the terminal exception once the configured attempt limit is exhausted
- Request headers include an application/json content-type automatically when payload content is supplied without an explicit header

## Enforcement

- Verified by: Automated unit test execution validating client retry backoff, attempt exhaustion, and exception filtering
- Verified by: Peer code review confirming that outbound HTTP calls consume the resilient client wrapper rather than raw unadorned HTTP clients
- Violation handling: Direct use of unadorned HTTP request methods without resilience decoration fails code review and requires refactoring to the centralized client boundary
- Exception process: Submit an architectural exception request detailing why the endpoint requires specialized transport handling or bypass of the centralized retry decorator
- Exception process: Document the approved exception and provide custom retry predicate justification at the caller boundary
