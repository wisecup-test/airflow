---
version: 2
name: cross-cutting-plugin-server-implementations-register-t-7096
description: "Adoption of HashiCorp go-plugin for External DAG Bundle Protocol Execution: Plugin Server Implementations Register Their Grpc"
metadata:
  actual:
    adr_id: 7096b598-f21a-45c6-8f65-db4bb4fdc479
    status: accepted
    candidate_group_id: 42449946-8874-4de8-a745-14dbba120442
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - dags/**
      - example/bundle/**
      - internal/protov1/**
      - pkg/api/**
      - pkg/bundles/shared/**
      - pkg/edgeapi/**
      - pkg/execution/**
      - pkg/logging/server/**
      - pkg/worker/**
---

# Adoption of HashiCorp go-plugin for External DAG Bundle Protocol Execution: Plugin Server Implementations Register Their Grpc

Scope: applies to `go-sdk/bundle/bundlev1/**`, `go-sdk/dags/**`, `go-sdk/example/bundle/**`, `go-sdk/internal/protov1/**`, `go-sdk/pkg/api/**`, `go-sdk/pkg/bundles/shared/**`, `go-sdk/pkg/edgeapi/**`, `go-sdk/pkg/execution/**`, `go-sdk/pkg/logging/server/**`, `go-sdk/pkg/worker/**`.

These rules are ALWAYS ACTIVE for:
- All external DAG bundle authoring and runtime integration protocols requiring out-of-process execution boundaries.
- Workload and metadata extraction tasks executed through external bundle binaries.

### Rules

- **R-PEA-001** MUST: Plugin server implementations MUST register their gRPC service implementations with the plugin server host and hand execution control to plugin.Serve.

### Verify

- Inspect the project repository manifest and build configuration to identify the plugin integration test runner, then execute the suite to verify subprocess launch, handshake negotiation, and gRPC communication.
- Discover and execute the protocol compliance validation scripts to ensure all client and server implementations satisfy the bundle service specification.
- Run the repository static analysis and linting targets to confirm strict conformance with plugin interface contracts and error handling requirements.

**Accept when:**

- All external bundle plugin client and server test suites pass with successful subprocess handshake and execution.
- Static analysis confirms full compliance with the plugin interface specification without contract deviations.
- Context cancellation signals properly propagate to terminate running child workloads without process leaks.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites validating plugin lifecycle and contract execution.
- Verified by: Repository static analysis and architecture linting rules blocking direct in-process bundle loading.
- Verified by: Peer code review verifying adherence to plugin protocol boundaries and error translation standards.
- Violation handling: Pull requests attempting in-process execution of external bundle workloads are automatically blocked in continuous integration.
- Violation handling: Submissions with unhandled plugin boundary errors or missing process cleanup logic are rejected during code review.
- Exception process: Exceptions requiring direct in-process execution must be submitted to the architecture review board with performance profiling data and isolation impact assessments.
- Exception process: Approved exceptions must be documented in an architectural exception record with explicit review and deprecation dates.
</enforcement>
