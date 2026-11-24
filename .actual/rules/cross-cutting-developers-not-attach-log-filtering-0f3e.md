---
version: 2
name: cross-cutting-developers-not-attach-log-filtering-0f3e
description: "Subsystem Log Filtering via logging.getLogger and addFilter: Developers Not Attach Log Filtering Logic"
metadata:
  actual:
    adr_id: 0f3e03c7-bc5b-45ad-b28a-a961c1633eb3
    status: accepted
    candidate_group_id: 9b7a903b-d767-4ba2-9643-99dafc4b5473
    evidence:
      source: deep
  scope:
    root: docker-stack-docs
---

# Subsystem Log Filtering via logging.getLogger and addFilter: Developers Not Attach Log Filtering Logic

Scope: applies to `docker-stack-docs/**`.

These rules are ALWAYS ACTIVE for:
- Subsystem log handling in automated documentation build scripts
- Integration points where external extension namespaces emit non-critical diagnostic noise

### Rules

- **R-L-001** SHOULD_NOT: Developers SHOULD_NOT attach log filtering logic directly to the root logger when the diagnostic noise originates from an isolated subordinate package.

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
