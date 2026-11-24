---
version: 2
name: cross-cutting-callers-not-invoke-platform-specific-5124
description: "Adoption of os/user Library Module for System User Inspection: Callers Not Invoke Platform Specific User"
metadata:
  actual:
    adr_id: 5124762e-29fd-4331-8d08-1d411cbba4f5
    status: accepted
    candidate_group_id: f8fd4360-092d-497b-a7b0-1749904dde52
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/api/**
      - pkg/worker/**
---

# Adoption of os/user Library Module for System User Inspection: Callers Not Invoke Platform Specific User

Scope: applies to `go-sdk/pkg/api/**`, `go-sdk/pkg/worker/**`.

These rules are ALWAYS ACTIVE for:
- Subsystem initialization routines requiring host user identification and execution context.
- Startup procedures performing platform environment validation.

### Rules

- **R-LM-001** SHOULD_NOT: Callers SHOULD NOT invoke platform-specific user lookup operations in performance-sensitive request loops.

### Verify

- Discover and execute the repository test suite to verify subsystem initialization completes without user lookup failures.
- Discover and execute the repository static analysis and linting checks to ensure os/user invocations adhere to error handling requirements.

**Accept when:**

- All initialization unit and integration tests pass without unhandled user lookup exceptions.
- Static analysis reports zero unhandled errors from os and os/user module calls in initialization routines.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test execution and automated static analysis checks.
- Verified by: Peer code review verifying os/user usage is restricted to initialization routines with appropriate error handling.
- Violation handling: Build pipeline failure on unhandled errors from system calls.
- Violation handling: Code review rejection for direct user lookup invocations outside designated initialization boundaries.
- Exception process: Documented architecture exception request reviewed and approved by the engineering architecture team.
</enforcement>
