---
version: 2
name: cross-cutting-components-initializing-local-filesystem-f687
description: "Direct Filesystem Data Access via os.Create for Task Log Storage: Components Initializing Local Filesystem Files Propagate"
metadata:
  actual:
    adr_id: f6875ca2-7a7e-44bb-8c01-d03e0f5c12b7
    status: accepted
    candidate_group_id: 2eeab7fd-3994-484d-aab8-1568352ed490
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/worker/**
---

# Direct Filesystem Data Access via os.Create for Task Log Storage: Components Initializing Local Filesystem Files Propagate

Scope: applies to `go-sdk/pkg/worker/**`.

These rules are ALWAYS ACTIVE for:
- Worker execution routines initializing output storage for running workloads.
- Filesystem data access operations derived from dynamic task metadata and runtime configuration.

### Rules

- **R-DAP-001** MUST: Components initializing local filesystem files MUST propagate and handle filesystem creation errors instead of panicking or failing silently.

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
