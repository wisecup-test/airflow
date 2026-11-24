---
version: 2
name: cross-cutting-worker-execution-components-creating-loc-43a0
description: "Direct Filesystem Data Access via os.Create for Task Log Storage: Worker Execution Components Creating Local Task"
metadata:
  actual:
    adr_id: 43a008e7-1d55-4190-9db2-28ffb2c9fa5e
    status: accepted
    candidate_group_id: 2eeab7fd-3994-484d-aab8-1568352ed490
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/worker/**
---

# Direct Filesystem Data Access via os.Create for Task Log Storage: Worker Execution Components Creating Local Task

Scope: applies to `go-sdk/pkg/worker/**`.

These rules are ALWAYS ACTIVE for:
- Worker execution routines initializing output storage for running workloads.
- Filesystem data access operations derived from dynamic task metadata and runtime configuration.

### Rules

- **R-DAP-001** MUST: Worker execution components creating local task log storage MUST verify and construct parent directories using explicit directory permissions prior to opening file descriptors.

### Verify

- Discover and execute the repository test suite to verify worker execution and task log initialization routines.
- Search the codebase for direct filesystem creation calls to confirm error handling and directory permission standards are maintained.

**Accept when:**

- Local file persistence routines successfully create designated storage paths and handle permission errors gracefully.
- Task log stream handlers write output to created file descriptors without panics or resource leaks.
- Verification routines confirm absence of hardcoded file system destinations across worker execution logic.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites verifying data access boundaries.
- Verified by: Peer code review confirming proper directory permission masks and error handling on file allocation.
- Violation handling: Pull requests introducing unhandled filesystem errors or hardcoded destination paths are blocked from merging.
- Violation handling: Static analysis findings highlighting direct unconfigured filesystem access must be remediated.
- Exception process: Submit an architectural variance request detailing alternate log transport mechanisms to the platform engineering team.
- Exception process: Obtain formal approval prior to bypassing local file system data access constraints.
</enforcement>
