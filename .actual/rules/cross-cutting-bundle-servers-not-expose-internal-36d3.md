---
version: 2
name: cross-cutting-bundle-servers-not-expose-internal-36d3
description: "bundlev1 Module: Bundle Servers Not Expose Internal Registry"
metadata:
  actual:
    adr_id: 36d38599-c68f-4ac3-9b05-6a7e8c79dcec
    status: accepted
    candidate_group_id: 3ee10499-1a34-4896-955a-78c69628a311
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - example/bundle/concurrentxcom/**
---

# bundlev1 Module: Bundle Servers Not Expose Internal Registry

Scope: applies to `go-sdk/bundle/bundlev1/**`, `go-sdk/example/bundle/concurrentxcom/**`.

These rules are ALWAYS ACTIVE for:
- All Go SDK bundle registry implementations and task definitions
- All gRPC plugin implementations serving bundle metadata and workload execution

### Rules

- **R-LM-001** MUST_NOT: Bundle servers MUST NOT expose internal bundle registry structures directly without mediation through LookupTask or the gRPC execution interface.

### Verify

- Discover the project test runner configuration from the repository manifest and execute all unit tests covering the bundle registry and server modules.
- Discover the project static analysis and linting configuration and run checks against all bundle registration and execution interface implementations.
- Discover the project concurrency and race-detector test command to verify thread-safe execution across bundle cache locking mechanisms.

**Accept when:**

- All unit tests covering bundle creation, task registration, and metadata reporting pass without failures.
- Static analysis validates that all bundle server implementations conform to the plugin interface contracts without unhandled errors.
- Concurrent execution tests pass with zero detected race conditions across registry cache locks.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites running race detection during continuous integration.
- Verified by: Static analysis and interface compliance linting in pull request pipelines.
- Verified by: Mandatory code review by core module maintainers.
- Violation handling: Pull requests introducing unmediated bundle access or missing synchronization locks will fail automated checks and be blocked from merge.
- Exception process: Exceptions require architecture review and documented approval by the SDK maintainers prior to merging deviations from bundle registry interfaces.
</enforcement>
