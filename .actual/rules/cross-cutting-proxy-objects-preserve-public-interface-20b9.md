---
version: 2
name: cross-cutting-proxy-objects-preserve-public-interface-20b9
description: "Lazy Object Proxy Adoption for Deferred Execution and Dynamic Loading: Proxy Objects Preserve Public Interface Contract"
metadata:
  actual:
    adr_id: 20b92ee7-ccf9-40a9-a2f1-08b2229dc9e8
    status: accepted
    candidate_group_id: 5e640ccb-a034-4e97-abe5-dcbcfc5b31b5
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Lazy Object Proxy Adoption for Deferred Execution and Dynamic Loading: Proxy Objects Preserve Public Interface Contract

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Registration and resolution of command-line interface commands, arguments, and help formatters
- Definition and loading of dialect-specific database migration data types

### Rules

- **R-LM-001** MUST: All proxy objects MUST preserve the public interface contract of the wrapped object so consuming callers interact with the proxy identically to the target instance.

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
