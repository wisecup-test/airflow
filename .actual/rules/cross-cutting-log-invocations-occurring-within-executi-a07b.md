---
version: 2
name: cross-cutting-log-invocations-occurring-within-executi-a07b
description: "Adoption of log/slog as Core Structured Logging Module: Log Invocations Occurring Within Execution Context"
metadata:
  actual:
    adr_id: a07b2bcc-8c1f-4465-8e59-f1527b6f123b
    status: accepted
    candidate_group_id: e42e41fd-ce27-49fc-9568-0f2b23e0bc47
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - edge/**
      - example/bundle/**
      - pkg/bundles/shared/**
      - pkg/config/**
      - pkg/execution/**
      - pkg/logging/**
      - pkg/worker/**
---

# Adoption of log/slog as Core Structured Logging Module: Log Invocations Occurring Within Execution Context

Scope: applies to `go-sdk/bundle/bundlev1/**`, `go-sdk/edge/**`, `go-sdk/example/bundle/**`, `go-sdk/pkg/bundles/shared/**`, `go-sdk/pkg/config/**`, `go-sdk/pkg/execution/**`, `go-sdk/pkg/logging/**`, `go-sdk/pkg/worker/**`.

These rules are ALWAYS ACTIVE for:
- All internal and public packages comprising the worker runtime, execution supervisor, communication dispatchers, and plugin servers.
- Diagnostic, operational, and task workload event generation across all execution components.

### Rules

- **R-LM-001** MUST: Log invocations occurring within an execution context MUST use context-aware logger methods to propagate cancellation and trace identifiers across asynchronous runtime boundaries.

### Verify

- Discover and execute the repository unit test suite across all packages to verify logging handlers and bridge behaviors.
- Discover and execute the repository static analysis and linting tools to check for banned logging imports and verify structured attribute compliance.

**Accept when:**

- All packages compile and pass repository test suites without importing unauthorized logging packages.
- All log emission call sites supply structured key-value attributes and pass static analysis checks.
- Custom handler implementations multiplex and route log records to expected output destinations during component verification.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration lint checks that flag imports of disallowed logging packages.
- Verified by: Code review inspection ensuring structured attribute usage and context propagation.
- Violation handling: Pull requests containing disallowed logging imports or unstructured logging calls will be blocked by automated continuous integration gates.
- Violation handling: Violations discovered in main branches must be remediated immediately via prioritized refactoring tasks.
- Exception process: Submit an architectural exception request detailing technical constraints requiring alternative logging behavior.
- Exception process: Obtain formal sign-off from the technical lead and record exception justification in component documentation.
</enforcement>
