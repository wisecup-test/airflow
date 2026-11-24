---
version: 2
name: cross-cutting-components-isolate-direct-calls-user-358a
description: "Adoption of os/user Library Module for System User Inspection: Components Isolate Direct Calls User Modules"
metadata:
  actual:
    adr_id: 358ae729-78a9-45ac-b62b-118669441e94
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

# Adoption of os/user Library Module for System User Inspection: Components Isolate Direct Calls User Modules

Scope: applies to `go-sdk/pkg/api/**`, `go-sdk/pkg/worker/**`.

These rules are ALWAYS ACTIVE for:
- Subsystem initialization routines requiring host user identification and execution context.
- Startup procedures performing platform environment validation.

### Rules

- **R-LM-001** MUST: Components MUST isolate direct calls to os and os/user modules within initialization boundaries rather than scattering user identity resolution across downstream business logic.

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
