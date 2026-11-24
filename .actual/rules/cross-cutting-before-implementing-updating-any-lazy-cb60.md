---
version: 2
name: cross-cutting-before-implementing-updating-any-lazy-cb60
description: "Lazy Object Proxy Adoption for Deferred Execution and Dynamic Loading: Before Implementing Updating Any Lazy Proxy"
metadata:
  actual:
    adr_id: cb60dcce-0aa0-4af0-9244-c005227b5278
    status: accepted
    candidate_group_id: 5e640ccb-a034-4e97-abe5-dcbcfc5b31b5
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Lazy Object Proxy Adoption for Deferred Execution and Dynamic Loading: Before Implementing Updating Any Lazy Proxy

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Registration and resolution of command-line interface commands, arguments, and help formatters
- Definition and loading of dialect-specific database migration data types

### Rules

- **R-LM-001** MUST: Before implementing or updating any lazy proxy integration, the consumer MUST inspect the repository lock artifact to determine the exact resolved version of lazy_object_proxy and verify that target APIs conform to that resolved version.

### Verify

- Discover the repository test runner from project configuration and execute tests covering command parsing and database migration type resolution.
- Discover the repository static analysis or linting tool and run checks across modules that implement deferred proxy wrappers to verify interface consistency.

**Accept when:**

- All unit and integration tests for command-line parsing and database dialect type resolution pass without premature module loading.
- Startup latency benchmarks demonstrate deferred loading behavior across top-level parser invocation.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites verifying deferred execution and proxy resolution behavior.
- Verified by: Peer code reviews checking that command definitions and dialect types do not introduce eager imports.
- Violation handling: Pull requests introducing eager imports in command parsing or migration configuration are flagged and required to wrap targets in lazy proxies.
- Exception process: Architectural review and exception approval required when high-frequency execution loops demonstrate measurable overhead from proxy indirection.
</enforcement>
