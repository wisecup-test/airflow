---
version: 2
name: cross-cutting-modules-not-emit-diagnostic-messages-f426
description: "Hierarchical Logger Resolution via logging.getLogger: Modules Not Emit Diagnostic Messages Directly"
metadata:
  actual:
    adr_id: f4264526-4ba5-4786-aaf3-0b3ca1363ef1
    status: accepted
    candidate_group_id: 1080f633-225d-40cc-b490-c069853c8824
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/docs/**
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Hierarchical Logger Resolution via logging.getLogger: Modules Not Emit Diagnostic Messages Directly

Scope: applies to `devel-common/src/docs/**`, `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- All source modules within documentation extensions, performance benchmarking suites, and testing utility frameworks requiring log generation or stream inspection.

### Rules

- **R-L-001** MUST_NOT: Modules MUST_NOT emit diagnostic messages directly to standard output or standard error streams using raw print calls or unnamespaced root loggers.

### Verify

- Discover the repository test runner script from the configuration files and execute the test harness suite to verify logging capture and stream isolation behavior.
- Inspect source modules across the repository using the project static analysis and linting routines to confirm that all diagnostic logging uses standard module logger instances.

**Accept when:**

- All modules instantiate loggers through standard library logger resolution with module-scoped names.
- Automated test suites pass without unhandled logger handler leaks or stream capture collisions.
- No raw print statements are detected in non-CLI internal utility modules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration lint checks inspecting logger instantiation patterns.
- Verified by: Peer code review during pull request inspection.
- Violation handling: Pull requests containing unapproved direct stream printing or root logger invocations will fail verification gates and require remediation.
- Exception process: Exceptions must be submitted via architectural review with explicit justification for raw stream handling.
</enforcement>
