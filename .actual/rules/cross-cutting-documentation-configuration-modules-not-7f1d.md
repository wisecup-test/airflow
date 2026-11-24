---
version: 2
name: cross-cutting-documentation-configuration-modules-not-7f1d
description: "docs.provider_conf Configuration via os.environ: Documentation Configuration Modules Not Expose Secret"
metadata:
  actual:
    adr_id: 7f1dc62a-1c6e-4d4a-961f-54eb47ce669e
    status: accepted
    candidate_group_id: 92240ca2-19a9-487f-bcbb-b1ae9a1fde74
    evidence:
      source: deep
  scope:
    root: providers/apache/beam
    paths:
      - docs/**
---

# docs.provider_conf Configuration via os.environ: Documentation Configuration Modules Not Expose Secret

Scope: applies to `providers/apache/beam/docs/**`.

These rules are ALWAYS ACTIVE for:
- Documentation configuration modules that declare provider metadata and build configurations.

### Rules

- **R-LM-001** MUST_NOT: Documentation configuration modules MUST_NOT expose secret credentials or sensitive tokens when querying os.environ for build metadata.

### Verify

- echo "Discover and execute repository configuration verification scripts"
- echo "Discover and execute documentation build validation routines"

**Accept when:**

- Documentation configuration builds complete without unhandled missing environment variable exceptions.
- Static analysis checks confirm environment variable reads follow safe access patterns.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks that execute documentation builds under clean environments.
- Verified by: Code review validation for any modifications to documentation configuration files.
- Violation handling: Pull requests failing documentation build steps are blocked from merging.
- Violation handling: Direct unsafe environment lookups without validation are flagged during code review.
- Exception process: Exceptions for static or mock configurations require approval from documentation maintainers and documentation in the configuration file.
</enforcement>
