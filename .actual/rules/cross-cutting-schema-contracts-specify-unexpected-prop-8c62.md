---
version: 2
name: cross-cutting-schema-contracts-specify-unexpected-prop-8c62
description: "Pydantic Schema Contracts for Public External API Integration: Schema Contracts Specify Unexpected Property Handling"
metadata:
  actual:
    adr_id: 8c62de5a-0ea5-462e-a43b-33ba4d856a41
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

# Pydantic Schema Contracts for Public External API Integration: Schema Contracts Specify Unexpected Property Handling

Scope: applies to `airflow-ctl/docs/**`, `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- Public external API client request payload modeling.
- External API server response deserialization and validation.
- Client operations exchanging structured JSON contracts with external service endpoints.

### Rules

- **R-PEA-001** MUST: Schema contracts MUST specify unexpected property handling through ConfigDict configuration, declaring whether additional attributes are explicitly accepted or forbidden.

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
