---
version: 2
name: cross-cutting-high-level-domain-clients-wrap-0d48
description: "google.golang.org/grpc Remote Procedure Call Integration: High Level Domain Clients Wrap Low"
metadata:
  actual:
    adr_id: 0d482a57-a108-4800-881f-7db37c7c9035
    status: accepted
    candidate_group_id: 18de2962-f355-48c9-8bce-e40459c59a7a
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/bundlev1client/**
      - internal/protov1/**
---

# google.golang.org/grpc Remote Procedure Call Integration: High Level Domain Clients Wrap Low

Scope: applies to `go-sdk/bundle/bundlev1/bundlev1client/**`, `go-sdk/internal/protov1/**`.

These rules are ALWAYS ACTIVE for:
- Components implementing remote inter-process plugin contracts and bundle communication layers
- Service endpoints and client adapters executing across decoupled runtime processes

### Rules

- **R-LM-001** SHOULD: High-level domain clients SHOULD wrap low-level remote procedure call responses into domain-specific data structures rather than leaking generated transport types across application boundaries.

### Verify

- Discover and run the project's dependency verification script to confirm that the resolved remote procedure call library aligns with the repository lock artifact.
- Discover and execute the project's static analysis suite to verify compliance with remote procedure call client and server registration standards.
- Discover and run the test suite covering bundle client communication to ensure remote procedure calls succeed under defined context deadlines.

**Accept when:**

- All inter-process bundle client operations resolve successfully via the configured remote procedure call transport.
- Remote calls gracefully handle timeout and cancellation when context cancellation triggers.
- Static analysis and repository test suites pass without reported violations or unimplemented stubs.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline executing repository test and linting suites.
- Verified by: Peer code review verifying adherence to remote procedure call contract boundaries and error handling rules.
- Violation handling: Pull requests violating remote procedure call registration or context propagation rules must be blocked from merging until corrected.
- Violation handling: Unregistered service methods or improper status code usage flagged during automated checks trigger build failure.
- Exception process: Formal request submitted to the architecture review team detailing rationale and test isolation proof for alternative transport.
- Exception process: Document approved exceptions in the repository decision log.
</enforcement>
