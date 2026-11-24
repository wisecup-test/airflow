---
version: 2
name: cross-cutting-before-implementing-network-dependencies-9677
description: "Go net Library for Network Transport: Before Implementing Network Dependencies Developers Discover"
metadata:
  actual:
    adr_id: 967736b8-148b-4305-9e00-a3506a90f3c2
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

# Go net Library for Network Transport: Before Implementing Network Dependencies Developers Discover

Scope: applies to `go-sdk/pkg/edgeapi/**`, `go-sdk/pkg/execution/**`.

These rules are ALWAYS ACTIVE for:
- Network transport, socket communication, and client-server request handling across software development kit packages

### Rules

- **R-LM-001** MUST: Before implementing network dependencies, developers MUST discover the dependency manifest and verify active runtime and dependency lock specifications.

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
