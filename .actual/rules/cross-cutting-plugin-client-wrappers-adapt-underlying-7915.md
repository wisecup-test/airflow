---
version: 2
name: cross-cutting-plugin-client-wrappers-adapt-underlying-7915
description: "Adopt github.com/hashicorp/go-plugin for Out-of-Process gRPC Plugin Architecture: Plugin Client Wrappers Adapt Underlying Grpc"
metadata:
  actual:
    adr_id: 7915653b-389e-4381-afca-fe634ed4e5a8
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

# Adopt github.com/hashicorp/go-plugin for Out-of-Process gRPC Plugin Architecture: Plugin Client Wrappers Adapt Underlying Grpc

Scope: applies to `go-sdk/bundle/bundlev1/bundlev1client/**`, `go-sdk/pkg/bundles/shared/**`.

These rules are ALWAYS ACTIVE for:
- All bundle client and server implementations interacting across process boundaries.
- Plugin extension points providing metadata retrieval or workload execution capabilities.

### Rules

- **R-LM-001** MUST: Plugin client wrappers MUST adapt underlying gRPC service contracts into native SDK interfaces, propagating context cancellation and transforming transport errors.

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
