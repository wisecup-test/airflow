---
version: 2
name: cross-cutting-subsystem-log-suppression-implemented-ac-574f
description: "Subsystem Log Filtering via logging.getLogger and addFilter: Subsystem Log Suppression Implemented Acquiring Target"
metadata:
  actual:
    adr_id: 574f6f39-eb35-47cc-8526-3ce51f5b1b77
    status: accepted
    candidate_group_id: 9b7a903b-d767-4ba2-9643-99dafc4b5473
    evidence:
      source: deep
  scope:
    root: docker-stack-docs
---

# Subsystem Log Filtering via logging.getLogger and addFilter: Subsystem Log Suppression Implemented Acquiring Target

Scope: applies to `docker-stack-docs/**`.

These rules are ALWAYS ACTIVE for:
- Subsystem log handling in automated documentation build scripts
- Integration points where external extension namespaces emit non-critical diagnostic noise

### Rules

- **R-L-001** MUST: Subsystem log suppression MUST be implemented by acquiring the target logger namespace via logging.getLogger and registering a custom filter callback using addFilter rather than altering global root logger thresholds.

### Verify

- Discover the project documentation build script from the repository configuration and execute it to verify that build logs run cleanly without targeted third-party warning cascades.
- Discover the test runner from the repository manifest and execute the test suite covering documentation utilities to validate filter predicate logic.

**Accept when:**

- The documentation build execution completes with zero spurious warnings from the targeted third-party namespace.
- Standard error output retains all legitimate diagnostic and failure warnings emitted by core modules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration documentation build log inspection
- Verified by: Pull request peer review of logging configuration changes
- Violation handling: Build pipeline failure on unhandled diagnostic errors
- Violation handling: Rejection of pull requests introducing global log suppression in localized scripts
- Exception process: Architectural review and exception approval recorded in tracking tickets when external tools lack structured logger integration.
</enforcement>
