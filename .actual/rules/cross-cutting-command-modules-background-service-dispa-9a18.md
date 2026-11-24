---
version: 2
name: cross-cutting-command-modules-background-service-dispa-9a18
description: "Adoption of the signal Module for Asynchronous Subprocess Lifecycle and Interruption Management: Command Modules Background Service Dispatchers That"
metadata:
  actual:
    adr_id: 9a18b850-a075-41b7-bc94-f01d4b81e6f6
    status: accepted
    candidate_group_id: a8ff5545-6dd5-4cf4-9da1-36325c38f915
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Adoption of the signal Module for Asynchronous Subprocess Lifecycle and Interruption Management: Command Modules Background Service Dispatchers That

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Command-line modules that initiate parallel subprocesses, worker pools, or background daemon servers.
- Utility routines responsible for orchestrating external process lifecycles and process group cleanup.

### Rules

- **R-LM-001** MUST: Command modules and background service dispatchers that spawn child processes or worker pools MUST adopt the standard signal module to intercept system termination and interruption signals.

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
