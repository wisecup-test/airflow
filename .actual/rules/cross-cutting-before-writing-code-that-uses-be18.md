---
version: 2
name: cross-cutting-before-writing-code-that-uses-be18
description: "Adopt github.com/hashicorp/go-plugin for Out-of-Process gRPC Plugin Architecture: Before Writing Code That Uses Updates"
metadata:
  actual:
    adr_id: be182142-7c07-4f40-9a50-e7a8c754066a
    status: accepted
    candidate_group_id: f4170281-3dae-4443-8ab0-e6ccca575b75
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/bundlev1client/**
      - pkg/bundles/shared/**
---

# Adopt github.com/hashicorp/go-plugin for Out-of-Process gRPC Plugin Architecture: Before Writing Code That Uses Updates

Scope: applies to `go-sdk/bundle/bundlev1/bundlev1client/**`, `go-sdk/pkg/bundles/shared/**`.

These rules are ALWAYS ACTIVE for:
- All bundle client and server implementations interacting across process boundaries.
- Plugin extension points providing metadata retrieval or workload execution capabilities.

### Rules

- **R-LM-001** MUST: Before writing code that uses or updates github.com/hashicorp/go-plugin or associated transport libraries, the consumer MUST inspect the repository dependency manifest and authoritative lock artifact to verify the exact resolved version against public API documentation.

### Verify

- Discover and run the repository build script to confirm successful compilation of all plugin interfaces and client implementations.
- Execute the repository test suite through the project test runner to validate plugin handshake negotiation and client-server gRPC communication contracts.

**Accept when:**

- All plugin client and server interfaces compile without error against the resolved plugin library.
- Handshake negotiation and gRPC bundle client methods pass all automated unit and integration tests.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated repository build and test pipeline checks validating plugin handshake and client implementations.
- Verified by: Architectural and code review of all pull requests introducing new plugin contracts or modifying plugin lifecycle handling.
- Violation handling: Pull requests implementing non-standard plugin transports or direct in-process loading will be blocked in code review.
- Violation handling: Violating code must be refactored to conform to the established go-plugin gRPC interface pattern.
- Exception process: Submit an architectural review request detailing the latency or deployment constraints that prevent using out-of-process gRPC plugins.
- Exception process: Obtain formal sign-off from the technical lead prior to introducing any alternative IPC or in-process extension mechanism.
</enforcement>
