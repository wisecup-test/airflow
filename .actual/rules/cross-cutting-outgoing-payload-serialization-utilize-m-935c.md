---
version: 2
name: cross-cutting-outgoing-payload-serialization-utilize-m-935c
description: "Pydantic Schema Contracts for Public External API Integration: Outgoing Payload Serialization Utilize Model Dump"
metadata:
  actual:
    adr_id: 935c85e0-a93f-4308-9a8c-93b0bc7684be
    status: accepted
    candidate_group_id: 9614f4da-7437-49e3-ac2e-7c68e71a4f21
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/**
---

# Pydantic Schema Contracts for Public External API Integration: Outgoing Payload Serialization Utilize Model Dump

Scope: applies to `airflow-ctl/docs/**`, `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- Public external API client request payload modeling.
- External API server response deserialization and validation.
- Client operations exchanging structured JSON contracts with external service endpoints.

### Rules

- **R-PEA-001** MUST: Outgoing API payload serialization MUST utilize model_dump with explicit serialization mode parameters rather than manual dictionary construction.

### Verify

- Discover and run the project static type validation commands to ensure schema contract compliance across all API client modules.
- Discover and execute the integration test suite validating serialization and deserialization against API contract models.

**Accept when:**

- All external API request and response data structures are validated and serialized through Pydantic contract models.
- No direct manipulation of unvalidated API response dictionaries occurs in client integration operations.
- Automated verification and static contract analysis suites pass across the integration test suite.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static contract checking and type analysis during continuous integration.
- Verified by: Peer code review on all pull requests modifying API integration client modules.
- Verified by: Integration test suites verifying payload serialization and deserialization against mock service endpoints.
- Violation handling: Pull requests containing untyped API payload access or unvalidated network dictionaries are blocked by continuous integration.
- Violation handling: Violations detected in review must be refactored into typed contract schemas prior to merge.
- Violation handling: Unvalidated boundary calls discovered in releases trigger priority remediation issues.
- Exception process: Submit an architectural request justifying why untyped payload exchange is necessary for a specific endpoint.
- Exception process: Obtain sign-off from API architecture maintainers and provide fallback boundary sanitization logic.
- Exception process: Document the exception scope and maintain an issue tracking migration to typed contract schemas.
</enforcement>
