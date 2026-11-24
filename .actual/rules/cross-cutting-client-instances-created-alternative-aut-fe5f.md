---
version: 2
name: cross-cutting-client-instances-created-alternative-aut-fe5f
description: "Resty v3 HTTP Client Adoption for API SDK Clients: Client Instances Created Alternative Authorization Credentials"
metadata:
  actual:
    adr_id: fe5f7f4e-be1a-49e6-9459-4ae7eaaba0f9
    status: accepted
    candidate_group_id: 9296ec27-893e-4f9f-89aa-328568a4ded1
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/api/**
---

# Resty v3 HTTP Client Adoption for API SDK Clients: Client Instances Created Alternative Authorization Credentials

Scope: applies to `go-sdk/pkg/api/**`.

These rules are ALWAYS ACTIVE for:
- All HTTP client implementations, functional client options, and client middleware within the API SDK.
- All mock interfaces and test doubles representing API service sub-clients.

### Rules

- **R-LM-001** MUST: Client instances created with alternative authorization credentials MUST clone headers and client configurations from the parent instance without mutating the existing client.

### Verify

- Discover the project repository build definition and execute the primary package compilation command to confirm all client interfaces and mock definitions compile cleanly.
- Discover and execute the repository unit and integration test suite to verify client mock expectations and HTTP transport contract adherence.

**Accept when:**

- Client constructors configure base URLs, middleware chains, and headers through the designated HTTP client abstraction.
- Service client contracts and test mocks expose uniform dual-method signatures returning domain responses alongside the standardized HTTP client response type.
- Verification directives executed across the repository demonstrate zero compilation errors or unmocked client contract mismatches.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis and module dependency checks in the continuous integration pipeline.
- Verified by: Interface compliance checks validating that client mocks implement standardized dual-response signatures.
- Verified by: Mandatory peer review on all additions or modifications to API client packages.
- Violation handling: Pull requests introducing unapproved HTTP transport libraries or bypassing client option patterns will be blocked by automated checks.
- Violation handling: Incompatible client interface signatures failing mock assertions must be refactored prior to merge.
- Exception process: Submit an architectural change request detailing why the standard HTTP client library cannot support the specific protocol requirements.
- Exception process: Obtain explicit approval from the architectural review team prior to introducing divergent transport implementations.
- Exception process: Document the exception and isolate any non-standard transport within an internal adapter package.
</enforcement>
