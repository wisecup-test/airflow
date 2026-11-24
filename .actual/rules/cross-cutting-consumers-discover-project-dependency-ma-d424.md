---
version: 2
name: cross-cutting-consumers-discover-project-dependency-ma-d424
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

Scope: applies to `dev/breeze/src/airflow_breeze/utils/**`.

These rules are ALWAYS ACTIVE for:
- Local development utility scripts and integration test harnesses managing cluster services
- Socket listener initialization and health check verification routines

### Rules

- **R-A-001** MUST: Consumers MUST discover the project dependency manifest and lock artifact to verify the exact resolved version of any third-party networking library before implementation.

### Verify

- Discover and run the project integration test suite targeting cluster management and socket utility modules.
- Execute the repository code linter and security scanner to inspect network listener bindings.

**Accept when:**

- All unit and integration tests for local network and cluster utilities pass without socket binding errors.
- Network listener verification confirms that local testing endpoints bind exclusively to loopback interfaces.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites and static analysis checks.
- Verified by: Peer code review verification during pull request inspection.
- Violation handling: Code binding sockets to non-loopback addresses in local utilities will be blocked during automated review.
- Violation handling: Pull requests failing socket lifecycle or port allocation tests must be remediated prior to merge.
- Exception process: Exceptions requiring wildcard binding must be submitted to the security and architecture review group with architectural justification.
</enforcement>
