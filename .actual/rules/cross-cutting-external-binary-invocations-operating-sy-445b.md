---
version: 2
name: cross-cutting-external-binary-invocations-operating-sy-445b
description: "subprocess Module Adoption for System Process and External Command Execution: External Binary Invocations Operating System Command"
metadata:
  actual:
    adr_id: 445b0640-c3e2-4dfc-8260-08bdf1270b10
    status: accepted
    candidate_group_id: d6efc215-c0b0-4dfe-a102-f4a10c857dda
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# subprocess Module Adoption for System Process and External Command Execution: External Binary Invocations Operating System Command

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Modules and components responsible for dispatching external operating system binaries and managing command-line processes.
- Security integration routines executing external authentication and credential refresh tools.

### Rules

- **R-LM-001** MUST: All external binary invocations, operating system command dispatches, and child process supervisions MUST be orchestrated through the subprocess module.

### Verify

- Discover and execute the project verification test runner from the root manifest to validate process execution contracts.
- Discover and run the static analysis and security scanning suite from the repository configuration to ensure command vector safety.

**Accept when:**

- All discovered unit and integration test suites for process invocation execute and terminate with a successful exit status.
- Automated static analysis and security linters pass with zero detected instances of unquoted shell command construction or unsafe process invocations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static security scanning and linters configured within continuous integration pipelines.
- Verified by: Peer code review validating adherence to process execution boundaries and input sanitization conventions.
- Violation handling: Pull requests containing unquoted command vectors or direct shell invocations are blocked from merging.
- Violation handling: Violations identified in existing modules must be refactored to use standard subprocess APIs with explicit argument sanitization.
- Exception process: Architectural review and security sign-off documented in the project issue tracker justifying why standard process execution interfaces cannot be used.
</enforcement>
