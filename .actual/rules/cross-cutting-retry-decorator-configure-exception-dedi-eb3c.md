---
version: 2
name: cross-cutting-retry-decorator-configure-exception-dedi-eb3c
description: "httpx.Client Request Resilience via @retry Decorator: Retry Decorator Configure Exception Dedicated Predicate"
metadata:
  actual:
    adr_id: eb3c6da9-7383-4f5f-bc2c-0ee501e28f09
    status: accepted
    candidate_group_id: 2153aeca-29a1-40ed-8044-23e6130a1e1a
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/api/**
---

# httpx.Client Request Resilience via @retry Decorator: Retry Decorator Configure Exception Dedicated Predicate

Scope: applies to `airflow-ctl/src/airflowctl/api/**`.

These rules are ALWAYS ACTIVE for:
- Outbound HTTP API requests managed by the central client wrapper class
- Network transport calls targeting services subject to transient transport failures and rate limits

### Rules

- **R-ISP-001** MUST: The @retry decorator MUST configure retry_if_exception using a dedicated predicate to evaluate whether an encountered exception or response status qualifies for a retry attempt.

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
