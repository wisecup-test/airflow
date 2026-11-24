---
version: 2
name: cross-cutting-plugin-server-implementations-register-d-8f63
description: "HashiCorp go-plugin DAG Bundle Contract Architecture: Plugin Server Implementations Register Dagbundleserver Implementation"
metadata:
  actual:
    adr_id: 8f6371f0-6269-40e5-935b-32e70aca3048
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

# HashiCorp go-plugin DAG Bundle Contract Architecture: Plugin Server Implementations Register Dagbundleserver Implementation

Scope: applies to `go-sdk/bundle/bundlev1/**`, `go-sdk/dags/**`, `go-sdk/edge/**`, `go-sdk/example/bundle/**`, `go-sdk/internal/**`, `go-sdk/pkg/**`, `go-sdk/sdk/**`.

These rules are ALWAYS ACTIVE for:
- DAG bundle implementations providing task and DAG definitions to the Go SDK worker runtime.
- Out-of-process execution boundaries between the worker host and bundle plugins.

### Rules

- **R-PEA-001** MUST: Plugin server implementations MUST register the DagBundleServer implementation via plugin.Serve and expose GetMetadata and Execute endpoints to handle host requests across process boundaries.

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
