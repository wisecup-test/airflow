---
version: 2
name: cross-cutting-task-registration-registries-execution-h-d76e
description: "reflect Library for Dynamic Task Registration and Execution Dispatch: Task Registration Registries Execution Handlers Use"
metadata:
  actual:
    adr_id: d76e02a1-7b4f-4630-8298-908e3921e495
    status: accepted
    candidate_group_id: 1000b391-20e7-4fc6-89cf-29b84ab06b44
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - example/bundle/concurrentxcom/**
      - internal/protov1/**
---

# reflect Library for Dynamic Task Registration and Execution Dispatch: Task Registration Registries Execution Handlers Use

Scope: applies to `go-sdk/bundle/bundlev1/**`, `go-sdk/example/bundle/concurrentxcom/**`, `go-sdk/internal/protov1/**`.

These rules are ALWAYS ACTIVE for:
- Dynamic task registration and runtime execution dispatch components within workflow bundles.
- Protocol reflection adapters and message introspection boundaries.
- Concurrent workflow execution handlers dispatching dynamic calls.

### Rules

- **R-LM-001** MUST: Task registration registries and execution handlers MUST use the reflect library to dynamically inspect function signatures and invoke task functions at runtime.

### Verify

- bash -c "test -f \"\$(find . -maxdepth 2 -name '*manifest*' -o -name '*lock*' | head -n 1)\" && echo 'Manifest located'"
- bash -c "echo 'Discover and execute project test suite for dynamic task registration and invocation via repository build scripts'"

**Accept when:**

- Dynamic task registration and invocation test suites pass without runtime panic or unexpected type error.
- Concurrency and synchronization tests verify thread-safe execution under parallel task workloads.
- Reflection-based signature validation correctly rejects invalid argument signatures with descriptive errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test suite executing dynamic task registration and execution test cases.
- Verified by: Peer code review verifying adherence to reflection validation and panic avoidance guidelines.
- Violation handling: Pull requests bypassing signature validation or lacking defensive reflection error handling are rejected.
- Violation handling: Static analysis checks identifying unhandled dynamic invocation returns block integration.
- Exception process: Submit an architectural exception request detailing performance profiling benchmarks that necessitate bypassing dynamic reflection.
- Exception process: Obtain explicit sign-off from the architecture review board prior to introducing static bypass paths.
</enforcement>
