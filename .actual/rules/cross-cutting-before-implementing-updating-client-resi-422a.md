---
version: 2
name: cross-cutting-before-implementing-updating-client-resi-422a
description: "httpx.Client Request Resilience via @retry Decorator: Before Implementing Updating Client Resilience Code"
metadata:
  actual:
    adr_id: 422a12e3-ed2b-4aa6-ab2f-ce048fbf5bb2
    status: accepted
    candidate_group_id: 2153aeca-29a1-40ed-8044-23e6130a1e1a
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/api/**
---

# httpx.Client Request Resilience via @retry Decorator: Before Implementing Updating Client Resilience Code

Scope: applies to `airflow-ctl/src/airflowctl/api/**`.

These rules are ALWAYS ACTIVE for:
- Outbound HTTP API requests managed by the central client wrapper class
- Network transport calls targeting services subject to transient transport failures and rate limits

### Rules

- **R-ISP-001** MUST: Before implementing or updating client resilience code, the engineering team MUST discover the repository dependency manifest and lock artifact to verify the exact resolved versions of the HTTP client and retry libraries against official documentation.

### Verify

- discover test runner in project configuration and run unit tests validating client retry backoff
- discover test suite execution commands and execute tests verifying exception filtering and header defaults

**Accept when:**

- Client request invocations retry upon encountering eligible transient exceptions up to the configured attempt threshold
- Requests fail and reraise the terminal exception once the configured attempt limit is exhausted
- Request headers include an application/json content-type automatically when payload content is supplied without an explicit header

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated unit test execution validating client retry backoff, attempt exhaustion, and exception filtering
- Verified by: Peer code review confirming that outbound HTTP calls consume the resilient client wrapper rather than raw unadorned HTTP clients
- Violation handling: Direct use of unadorned HTTP request methods without resilience decoration fails code review and requires refactoring to the centralized client boundary
- Exception process: Submit an architectural exception request detailing why the endpoint requires specialized transport handling or bypass of the centralized retry decorator
- Exception process: Document the approved exception and provide custom retry predicate justification at the caller boundary
</enforcement>
