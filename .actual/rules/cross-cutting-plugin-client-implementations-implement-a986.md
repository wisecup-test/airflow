---
version: 2
name: cross-cutting-plugin-client-implementations-implement-a986
description: "HashiCorp go-plugin DAG Bundle Contract Architecture: Plugin Client Implementations Implement Grpcclient Interface"
metadata:
  actual:
    adr_id: a986d77a-af05-4c6a-b15f-17f36eeaf67a
    status: accepted
    candidate_group_id: a5a5e46e-a87d-41de-a477-56792ab9d164
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - dags/**
      - edge/**
      - example/bundle/**
      - internal/**
      - pkg/**
      - sdk/**
---

# HashiCorp go-plugin DAG Bundle Contract Architecture: Plugin Client Implementations Implement Grpcclient Interface

Scope: applies to `go-sdk/bundle/bundlev1/**`, `go-sdk/dags/**`, `go-sdk/edge/**`, `go-sdk/example/bundle/**`, `go-sdk/internal/**`, `go-sdk/pkg/**`, `go-sdk/sdk/**`.

These rules are ALWAYS ACTIVE for:
- DAG bundle implementations providing task and DAG definitions to the Go SDK worker runtime.
- Out-of-process execution boundaries between the worker host and bundle plugins.

### Rules

- **R-PEA-001** MUST: Plugin client implementations MUST implement the GRPCClient interface and delegate GetMetadata and ExecuteTaskWorkload operations to the underlying gRPC client stubs with context propagation.

### Verify

- Discover and run the test suite for plugin client and server contract implementations from the project repository configuration.
- Discover and execute the repository static analysis and linting checks to verify interface compliance.

**Accept when:**

- All plugin client and server test suites pass without contract mismatches or runtime panics.
- Subprocess lifecycle tests verify that plugins terminate cleanly upon host shutdown or context cancellation.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated contract testing and integration test suites executed during continuous integration.
- Verified by: Architecture and code reviews for modifications to bundle interfaces or gRPC definitions.
- Violation handling: Build failures triggered when contract signatures or plugin registrations deviate from the defined interface.
- Violation handling: Rejection of pull requests that bypass the out-of-process plugin boundary.
- Exception process: Architectural review submission detailing performance or operational justification for alternative execution models, requiring approval from the core SDK maintainers.
</enforcement>
