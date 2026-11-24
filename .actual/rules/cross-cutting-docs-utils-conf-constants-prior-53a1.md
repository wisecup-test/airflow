---
version: 2
name: cross-cutting-docs-utils-conf-constants-prior-53a1
description: "Internal Module Adoption: docs.utils.conf_constants: Prior Integrating Consuming Any Versioned External"
metadata:
  actual:
    adr_id: 53a10de8-c810-41ca-adf5-7fcc256195dc
    status: accepted
    candidate_group_id: fffe84cb-e2bd-4103-ad75-9d1c5b4d020f
    evidence:
      source: deep
  scope:
    root: docker-stack-docs
---

# Internal Module Adoption: docs.utils.conf_constants: Prior Integrating Consuming Any Versioned External

Scope: applies to `docker-stack-docs/**`.

These rules are ALWAYS ACTIVE for:
- Build configuration modules requiring shared documentation metadata and context generation.

### Rules

- **R-LM-001** MUST: Prior to integrating or consuming any versioned external dependency, the consumer MUST inspect the repository lock or resolution artifact to determine and verify the exact resolved version.

### Verify

- Discover and run the project documentation build and verification script to confirm configuration integrity.
- Discover and run the project static analysis or test suite to verify module import resolution.

**Accept when:**

- The documentation build pipeline completes without errors and correctly resolves navigation sidebars.
- Static verification confirms that all context helper invocations adhere to the shared module interfaces.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration documentation build checks.
- Verified by: Peer code review for changes affecting documentation configuration modules.
- Violation handling: Documentation build failures in continuous integration block merge requests.
- Violation handling: Code review rejection for hardcoded sidebar configurations.
- Exception process: Exceptions must be submitted via architectural review and documented in the repository configuration tracking log.
</enforcement>
