---
version: 2
name: cross-cutting-dialers-listener-configurations-configur-8015
description: "Go net Library for Network Transport: Dialers Listener Configurations Configure Explicit Timeouts"
metadata:
  actual:
    adr_id: 80152b4c-ec6d-416c-8158-2786e7eb858b
    status: accepted
    candidate_group_id: 21ebb723-83a4-4477-84af-f6b025953fd8
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/edgeapi/**
      - pkg/execution/**
---

# Go net Library for Network Transport: Dialers Listener Configurations Configure Explicit Timeouts

Scope: applies to `go-sdk/pkg/edgeapi/**`, `go-sdk/pkg/execution/**`.

These rules are ALWAYS ACTIVE for:
- Network transport, socket communication, and client-server request handling across software development kit packages

### Rules

- **R-LM-001** SHOULD: Dialers and listener configurations SHOULD configure explicit timeouts and keepalive intervals on raw network connections.

### Verify

- Discover and run the project static analysis and linting scripts to verify that network communications use approved standard library packages.
- Discover and execute the project test suite to validate network connection handling, retry mechanisms, and timeout behavior.

**Accept when:**

- Static analysis confirms all socket dialing and HTTP transport interactions utilize standard library networking packages without unapproved third-party networking imports.
- All unit and integration tests for client and server transport pass without connection leaks or unhandled network errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis and import inspection in continuous integration workflows
- Verified by: Mandatory peer code review for any changes introducing external network dependencies
- Violation handling: Pull requests introducing unauthorized third-party networking libraries will fail automated linting gates and be blocked from merge until remediated
- Exception process: Submit an architectural review request detailing technical justifications where standard library networking cannot meet performance or protocol requirements
</enforcement>
