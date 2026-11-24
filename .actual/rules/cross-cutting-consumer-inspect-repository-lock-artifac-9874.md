---
version: 2
name: cross-cutting-consumer-inspect-repository-lock-artifac-9874
description: "Adoption of the signal Module for Asynchronous Subprocess Lifecycle and Interruption Management: Consumer Inspect Repository Lock Artifact Determine"
metadata:
  actual:
    adr_id: 98744a4d-85a1-4f3f-b5a0-46f6de9319fd
    status: accepted
    candidate_group_id: a8ff5545-6dd5-4cf4-9da1-36325c38f915
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Adoption of the signal Module for Asynchronous Subprocess Lifecycle and Interruption Management: Consumer Inspect Repository Lock Artifact Determine

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Command-line modules that initiate parallel subprocesses, worker pools, or background daemon servers.
- Utility routines responsible for orchestrating external process lifecycles and process group cleanup.

### Rules

- **R-LM-001** MUST: The consumer MUST inspect the repository lock artifact to determine the exact resolved dependency versions and verify runtime module compatibility before implementing process management routines.

### Verify

- python3 -c 'import signal; assert hasattr(signal, "signal") and hasattr(signal, "SIGTERM")'
- find . -maxdepth 3 -type f \( -name "*test*" -o -name "*lint*" \) -exec echo "Discovered project verification runner: {}" \;

**Accept when:**

- All automated tests covering signal registration, process group termination, and signal handler teardown execute successfully.
- Static analysis confirms that custom signal handlers suppress benign operating system errors and avoid non-reentrant operations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites verifying clean child process termination under simulated termination and interrupt signals.
- Verified by: Continuous inspection and static analysis checking for compliant signal handler registration and process group management.
- Violation handling: Pull requests introducing subprocess dispatchers without signal handling and process group termination contracts will be blocked during review.
- Violation handling: Automated test failures caused by orphaned child processes or lingering tasks will trigger build rejection.
- Exception process: Exceptions must be submitted via an architecture review request detailing the alternative process supervisory mechanism and cleanup guarantees.
</enforcement>
