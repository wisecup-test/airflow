---
version: 2
name: cross-cutting-custom-middleware-that-tracks-request-0fec
description: "FastAPI Application Boundary Middleware Structuring: Custom Middleware That Tracks Request Metadata"
metadata:
  actual:
    adr_id: 0fecb47e-ade1-4444-9e50-4fb99d653b90
    status: accepted
    candidate_group_id: a4db21fa-e788-4d9d-bd4b-0e168259c57e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/**
---

# FastAPI Application Boundary Middleware Structuring: Custom Middleware That Tracks Request Metadata

Scope: applies to `airflow-core/src/airflow/api_fastapi/**`.

These rules are ALWAYS ACTIVE for:
- FastAPI application factories and sub-application entry points exposing HTTP service interfaces.
- Cross-cutting boundary protocols including request correlation tracing, authentication header inspection, access logging, and transport compression.

### Rules

- **R-ISP-001** SHOULD: Custom middleware that tracks request metadata SHOULD bind context variables prior to downstream execution and append corresponding tracking headers to the outgoing response.

### Verify

- Discover and run the project static analysis and linting scripts to verify that middleware registrations conform to application factory conventions.
- Discover and run the project test suite for HTTP service entry points to confirm request and response header propagation across all active middleware layers.

**Accept when:**

- All application factory definitions register required boundary middleware components through add_middleware in the specified sequence.
- Cross-cutting request correlation and security header handling are verified through automated integration tests without route-level intervention.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Static analysis and code review ensuring middleware registration in application factory routines.
- Verified by: Automated integration tests validating header propagation and response modification across service boundaries.
- Violation handling: Pull requests duplicating cross-cutting protocol logic in route handlers or bypassing standard middleware registration are rejected.
- Exception process: Architectural exceptions for endpoints requiring raw ASGI bypass or custom stream handling must be submitted for engineering review and documented in the repository.
</enforcement>
