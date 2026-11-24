---
version: 2
name: cross-cutting-components-interacting-secret-vault-inst-4cf2
description: "akeyless SDK Client Encapsulation and Token Caching: Components Interacting Secret Vault Instantiate Access"
metadata:
  actual:
    adr_id: 4cf21cf2-0f59-41c3-9bf7-a91f1643c8c7
    status: accepted
    candidate_group_id: 1ceda63a-82a9-466c-8a4b-9bfc7675f159
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/providers/akeyless/**
---

# akeyless SDK Client Encapsulation and Token Caching: Components Interacting Secret Vault Instantiate Access

Scope: applies to `providers/akeyless/src/airflow/providers/akeyless/**`.

These rules are ALWAYS ACTIVE for:
- Modules providing connection hooks, credential retrievers, or backend secrets storage integrating with the external vault service.
- Service adapters responsible for mediating authentication and secret exchange between internal workflows and external vault endpoints.

### Rules

- **R-IA-001** MUST: Components interacting with the secret vault MUST instantiate and access the external SDK client exclusively through a cached property returning the configured API client interface.

### Verify

- Discover the project test runner configuration from the repository manifest and execute unit test suites targeting secret backend and hook integrations.
- Discover the project static analysis and linting configuration from the repository root and run validation checks against integration boundary modules.

**Accept when:**

- All integration unit test suites validating secret retrieval and client initialization succeed against mocked and configured provider interfaces.
- Static analysis confirms that direct invocations of the external vault SDK remain confined to designated client wrapper boundaries.
- Token caching logic demonstrates proper expiration and renewal behavior during simulated repeated secret lookups.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test validation ensuring all secrets backend and hook operations invoke the encapsulated client property.
- Verified by: Code review gates verifying no unencapsulated external SDK calls are introduced outside adapter boundaries.
- Violation handling: Pull requests introducing direct external SDK calls outside adapter classes must be blocked by code review.
- Violation handling: Violations detected in runtime configurations must be refactored to route through approved hook or backend interfaces.
- Exception process: Submit an architectural review request detailing why the standard client wrapper cannot satisfy the integration requirement.
- Exception process: Document compensating safeguards for credential protection, token lifecycle management, and interface insulation in the review proposal.
</enforcement>
