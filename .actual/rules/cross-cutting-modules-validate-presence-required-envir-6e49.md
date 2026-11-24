---
version: 2
name: cross-cutting-modules-validate-presence-required-envir-6e49
description: "Runtime Configuration Access via os.environ: Modules Validate Presence Required Environment Keys"
metadata:
  actual:
    adr_id: 6e491b25-0303-4933-8761-9b8d8c2b28a2
    status: accepted
    candidate_group_id: 4e3b5fe1-c2db-4e83-985c-ca659d12de34
    evidence:
      source: deep
  scope:
    root: docker-stack-docs
---

# Runtime Configuration Access via os.environ: Modules Validate Presence Required Environment Keys

Scope: applies to `docker-stack-docs/**`.

These rules are ALWAYS ACTIVE for:
- Runtime execution and documentation configuration modules requiring external build parameterization.

### Rules

- **R-CEM-001** SHOULD: Modules SHOULD validate the presence of required environment keys at initialization rather than deferring lookups across execution.

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
