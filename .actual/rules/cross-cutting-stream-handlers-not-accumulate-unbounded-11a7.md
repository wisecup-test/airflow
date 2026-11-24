---
version: 2
name: cross-cutting-stream-handlers-not-accumulate-unbounded-11a7
description: "Adoption of io Module for Stream and Buffer Handling: Stream Handlers Not Accumulate Unbounded Payloads"
metadata:
  actual:
    adr_id: 11a7c661-15f6-4250-8c3c-7351576cd2a8
    status: accepted
    candidate_group_id: b65dc43f-90c3-41cf-81ba-10ebde282069
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of io Module for Stream and Buffer Handling: Stream Handlers Not Accumulate Unbounded Payloads

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Command-line interface data exporters, configuration formatting handlers, and task logging handlers performing stream I/O operations

### Rules

- **R-LM-001** SHOULD_NOT: Stream handlers SHOULD NOT accumulate unbounded stream payloads in memory buffers when the total payload size is indeterminate.

### Verify

- sh -c 'test -n "$(git status --porcelain 2>/dev/null)" || echo "Repository state checked; locate test execution scripts to validate stream components"'
- echo "Discover and execute project test suite from repository configuration scripts to verify stream contracts"

**Accept when:**

- Stream consumer components accept polymorphic io stream targets and pass automated test assertions with in-memory streams
- Log emission and CLI export routines flush stream buffers without unhandled resource warnings

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Static analysis rules detecting unbuffered or unclosed stream operations
- Verified by: Peer review of pull requests touching CLI output, configuration formatting, or logging handlers
- Verified by: Automated test suites exercising stream-based components with in-memory stream substitutes
- Violation handling: Pull requests directly coupling business logic to standard process outputs without stream abstractions are blocked at code review
- Violation handling: Stream leaks or unhandled flush operations identified during static analysis must be resolved before deployment
- Exception process: Submit an architectural exception request documenting the specific requirement preventing the use of standard stream abstractions
- Exception process: Obtain approval from component maintainers prior to merging non-compliant I/O implementations
</enforcement>
