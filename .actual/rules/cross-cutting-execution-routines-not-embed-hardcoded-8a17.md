---
version: 2
name: cross-cutting-execution-routines-not-embed-hardcoded-8a17
description: "Runtime Configuration Access via os.environ: Execution Routines Not Embed Hardcoded Environment"
metadata:
  actual:
    adr_id: 8a1773d1-8f02-4d59-9a10-b9b22e5fc93b
    status: accepted
    candidate_group_id: 4e3b5fe1-c2db-4e83-985c-ca659d12de34
    evidence:
      source: deep
  scope:
    root: docker-stack-docs
---

# Runtime Configuration Access via os.environ: Execution Routines Not Embed Hardcoded Environment

Scope: applies to `docker-stack-docs/**`.

These rules are ALWAYS ACTIVE for:
- Runtime execution and documentation configuration modules requiring external build parameterization.

### Rules

- **R-CEM-001** MUST_NOT: Execution routines MUST NOT embed hardcoded environment values when external runtime parameters govern build variations.

### Verify

- Discover and run the project test runner to execute configuration verification suites.
- Discover and run the repository linter to verify environment variable access patterns adhere to project standards.

**Accept when:**

- All configuration verification suites execute successfully with required environment parameters configured.
- Static analysis passes with zero unauthorized or unhandled environment variable lookup violations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline execution.
- Verified by: Peer review of configuration and execution scripts during change review.
- Violation handling: Changes introducing unvalidated or scattered direct environment lookups are rejected during review.
- Exception process: Exceptions require documented architectural justification and written approval from technical leads.
</enforcement>
