---
version: 2
name: cross-cutting-before-implementing-modifying-code-that-f119
description: "Adoption of os/user Library Module for System User Inspection: Before Implementing Modifying Code That References"
metadata:
  actual:
    adr_id: f119dcd8-0f18-42a9-84b4-94a794eeb184
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

# Adoption of os/user Library Module for System User Inspection: Before Implementing Modifying Code That References

Scope: applies to `go-sdk/pkg/api/**`, `go-sdk/pkg/worker/**`.

These rules are ALWAYS ACTIVE for:
- Subsystem initialization routines requiring host user identification and execution context.
- Startup procedures performing platform environment validation.

### Rules

- **R-LM-001** MUST: Before implementing or modifying code that references external or core modules, developers MUST discover the project dependency manifest and lock artifact to verify the active runtime environment matches the resolved dependency baseline.

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
