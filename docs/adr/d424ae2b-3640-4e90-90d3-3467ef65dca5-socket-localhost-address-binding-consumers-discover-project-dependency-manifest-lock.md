---
version: 2
name: d424ae2b-3640-4e90-90d3-3467ef65dca5-socket-localhost-address-binding-consumers-discover-project-dependency-manifest-lock
description: "Socket Localhost Address Binding: Consumers Discover Project Dependency Manifest Lock"
metadata:
  actual:
    adr_id: d424ae2b-3640-4e90-90d3-3467ef65dca5
    status: accepted
    candidate_group_id: 4fea730f-3b9e-4edb-9af9-61c756f980a4
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/utils/**
---

# Socket Localhost Address Binding: Consumers Discover Project Dependency Manifest Lock

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/utils/**`

## Context

- Local orchestration utilities require ephemeral socket port allocation and health check polling when provisioning development environments.
- Binding network services to arbitrary or wildcard network interfaces creates security risks by exposing internal testing endpoints.
- An implementation pattern in cluster management utilities binds socket listeners directly to loopback address 127.0.0.1 and registers allocated ports to prevent race conditions during health probes.

## Problem Statement

Development and testing automation utilities that provision local services require reliable port assignment and service health inspection without exposing internal endpoints to the local network or encountering port allocation conflicts.

## Decision

- **R-A-001** MUST: Consumers MUST discover the project dependency manifest and lock artifact to verify the exact resolved version of any third-party networking library before implementation.

## Policy Block

In scope:

- Local development utility scripts and integration test harnesses managing cluster services
- Socket listener initialization and health check verification routines

Out of scope:

- Production service deployment configurations
- Publicly facing network ingress interfaces

## Rationale

- Restricting socket binding to the loopback interface ensures that diagnostic and monitoring endpoints are inaccessible from external network interfaces.
- Tracking bound socket identifiers in an in-memory set mitigates port collision during concurrent cluster setup operations.
- Direct loopback communication minimizes attack surface and eliminates cross-host network dependency during automated local testing.

## Consequences

Positive:

- Prevents accidental external network exposure of cluster control and health monitoring endpoints during local execution.
- Provides deterministic local port allocation reducing conflicts across parallel integration testing workflows.

Negative:

- Inability to access cluster monitoring endpoints from external hosts without explicit port forwarding.
- In-memory socket tracking remains scoped to the local process lifecycle and does not guarantee multi-process synchronization.

## Alternatives

- Binding sockets to all available network interfaces on wildcard address 0.0.0.0 (rejected)
  Rejected because: Exposes internal test and management endpoints to the local area network, creating an unnecessary security vulnerability.
  When valid: Valid only for services specifically intended to accept remote incoming network traffic across external hosts.
- Relying on operating system dynamic port assignment without manual tracking (rejected)
  Rejected because: Lacks coordinated tracking for subsequent health check invocations, increasing collision risk during concurrent runs.
  When valid: Valid when services handle dynamic port reporting and self-registration automatically.

## Risks

- Local socket binding does not guarantee reservation across distinct concurrent processes running on the same host.
  Mitigation: Implement retry loops with randomized port selection and timeout limits during socket acquisition.
  Owner: engineering team
- Hardcoded loopback addressing fails in IPv6-only container environments.
  Mitigation: Abstract loopback address resolution to support dual-stack host configurations when necessary.
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
- Locate the networking utility modules within the repository to inspect existing socket binding patterns and port allocation registries.
- Ensure socket instances are handled within structured lifecycle blocks to guarantee that descriptors are released upon exit or failure.

## Continuation Context

Verify commands:

- Discover and run the project integration test suite targeting cluster management and socket utility modules.
- Execute the repository code linter and security scanner to inspect network listener bindings.

Accept when:

- All unit and integration tests for local network and cluster utilities pass without socket binding errors.
- Network listener verification confirms that local testing endpoints bind exclusively to loopback interfaces.

## Enforcement

- Verified by: Automated continuous integration test suites and static analysis checks.
- Verified by: Peer code review verification during pull request inspection.
- Violation handling: Code binding sockets to non-loopback addresses in local utilities will be blocked during automated review.
- Violation handling: Pull requests failing socket lifecycle or port allocation tests must be remediated prior to merge.
- Exception process: Exceptions requiring wildcard binding must be submitted to the security and architecture review group with architectural justification.
