---
version: 2
name: cross-cutting-application-client-layer-standardize-axi-3f49
description: "Axios HTTP Client Adoption for Service Request Layer: Application Client Layer Standardize Axios Via"
metadata:
  actual:
    adr_id: 3f49c436-0bbe-4670-9e80-293dbd00e284
    status: accepted
    candidate_group_id: a84f39eb-448d-4914-814e-b0e47809cb05
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Axios HTTP Client Adoption for Service Request Layer: Application Client Layer Standardize Axios Via

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Client-side service callers interacting with backend API endpoints.
- Client modules managing authentication tokens, request serialization, and event stream parsing.

### Rules

- **R-LM-001** MUST: The application client layer MUST standardize on Axios via the client instantiation pattern to manage HTTP transport boundaries across service client modules.

### Verify

- Discover the project script runner and execute the module build check to verify client compilation.
- Discover and run the project test suite covering API client requests and serialization utilities.
- Discover and execute the static analysis linter to confirm adherence to client instantiation rules.

**Accept when:**

- Client instantiation compiles without type errors and routes network traffic through configured client instances.
- All service calls fall back to the default client when an override client is omitted in options.
- Project test suites and static analysis checks pass with zero violations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis rules verifying that service callers do not bypass the client factory.
- Verified by: Peer code review ensuring new endpoint definitions utilize standard client parameter patterns.
- Violation handling: Pull requests containing direct unconfigured network calls or missing client fallback options are blocked in review.
- Violation handling: Non-compliant service definitions must be refactored to use the client factory and modular utility structure.
- Exception process: Exceptions for specialized network communication must be submitted with architectural rationale and reviewed by the lead maintainers.
</enforcement>
