---
version: 2
name: cross-cutting-consumer-discover-ecosystem-dependency-m-5a73
description: "Airflow BaseHook test_connection Contract Implementation: Consumer Discover Ecosystem Dependency Manifest Lock"
metadata:
  actual:
    adr_id: 5a73320d-cbf1-4dc6-9713-4ffb5d3d9f56
    status: accepted
    candidate_group_id: f8a7454a-ba33-4378-b37b-22d738843b53
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/providers/akeyless/hooks/**
---

# Airflow BaseHook test_connection Contract Implementation: Consumer Discover Ecosystem Dependency Manifest Lock

Scope: applies to `providers/akeyless/src/airflow/providers/akeyless/hooks/**`.

These rules are ALWAYS ACTIVE for:
- Custom hook classes extending BaseHook interfacing with external authentication and credential services

### Rules

- **R-TS-001** MUST: The consumer MUST discover the ecosystem dependency manifest and lock artifact to resolve the exact locked version of all provider and client libraries before implementation

### Verify

- Discover and run the project test suite targeting hook connection validation methods
- Discover and run static type checking across provider hook modules to validate method signatures

**Accept when:**

- Connection testing methods return a tuple containing a boolean flag and a descriptive string message
- Authentication exceptions are captured and formatted into error messages without raising unhandled exceptions

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated code review and continuous integration test suites
- Violation handling: Pull requests lacking required connectivity verification methods or exception handling must be revised prior to merge
- Exception process: Document justified exceptions in architectural review when integrating with third-party systems that do not expose interactive authentication
</enforcement>
