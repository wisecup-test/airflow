---
version: 2
name: cross-cutting-service-connection-helper-providers-enca-d3d1
description: "Internal API Boundary Encapsulation and Client Simulation in devel-common: Service Connection Helper Providers Encapsulate Authentication"
metadata:
  actual:
    adr_id: d3d18adf-4c2a-42ed-a14b-7e81b8f0524e
    status: accepted
    candidate_group_id: f3f441f7-7a8c-4ea8-bfe0-bc9941efe66f
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/docs/**
      - src/sphinx_exts/**
      - src/tests_common/**
---

# Internal API Boundary Encapsulation and Client Simulation in devel-common: Service Connection Helper Providers Encapsulate Authentication

Scope: applies to `devel-common/src/docs/**`, `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/**`.

These rules are ALWAYS ACTIVE for:
- Internal testing utilities, test helpers, and build extensions interacting with external client configurations or environment variables.
- Boundary adapters and client simulation harnesses defined within the devel-common package.

### Rules

- **R-IA-001** MUST: Service connection helper providers MUST encapsulate authentication metadata parsing and connection identifier resolution within dedicated provider functions.

### Verify

- manifest=$(find . -maxdepth 2 -name '*toml' -o -name '*json' | head -n 1) && [ -n "$manifest" ] && echo "Identified project manifest: $manifest"
- find . -type f -exec grep -l 'devel-common' {} + 2>/dev/null | head -n 10

**Accept when:**

- All test suites utilizing boundary client mocks execute successfully without network access.
- External configuration retrievals provide valid defaults when target environment variables are unset.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suite execution in isolated continuous integration environments without external network access.
- Verified by: Pull request code reviews ensuring external client calls adhere to internal boundary helpers.
- Violation handling: Direct unmocked external client connections in unit test suites trigger build failure.
- Violation handling: Unguarded environment lookups are rejected during peer review and static analysis.
- Exception process: Exceptions for live system integration tests require review and approval by architectural module owners.
</enforcement>
