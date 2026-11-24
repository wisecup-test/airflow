---
version: 2
name: cross-cutting-signal-handling-callbacks-not-invoke-0a18
description: "Adoption of the signal Module for Asynchronous Subprocess Lifecycle and Interruption Management: Signal Handling Callbacks Not Invoke Blocking"
metadata:
  actual:
    adr_id: 0a186a58-8696-45b7-a4cf-6013da274634
    status: accepted
    candidate_group_id: a8ff5545-6dd5-4cf4-9da1-36325c38f915
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Adoption of the signal Module for Asynchronous Subprocess Lifecycle and Interruption Management: Signal Handling Callbacks Not Invoke Blocking

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Command-line modules that initiate parallel subprocesses, worker pools, or background daemon servers.
- Utility routines responsible for orchestrating external process lifecycles and process group cleanup.

### Rules

- **R-LM-001** MUST_NOT: Signal handling callbacks MUST NOT invoke blocking input operations or re-entrant asynchronous dispatchers that can deadlock during signal processing.

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
