---
version: 2
name: cross-cutting-custom-filter-callbacks-attached-via-1c11
description: "Subsystem Log Filtering via logging.getLogger and addFilter: Custom Filter Callbacks Attached Via Addfilter"
metadata:
  actual:
    adr_id: 1c11935f-f167-40cc-9e20-a840b26c8a38
    status: accepted
    candidate_group_id: 9b7a903b-d767-4ba2-9643-99dafc4b5473
    evidence:
      source: deep
  scope:
    root: docker-stack-docs
---

# Subsystem Log Filtering via logging.getLogger and addFilter: Custom Filter Callbacks Attached Via Addfilter

Scope: applies to `docker-stack-docs/**`.

These rules are ALWAYS ACTIVE for:
- Subsystem log handling in automated documentation build scripts
- Integration points where external extension namespaces emit non-critical diagnostic noise

### Rules

- **R-L-001** MUST_NOT: Custom filter callbacks attached via addFilter MUST NOT discard log records globally without validating that the record namespace precisely matches the targeted subsystem.

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
