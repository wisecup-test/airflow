---
version: 2
name: cross-cutting-projects-generating-external-public-docu-0c66
description: "sphinx.autoapi Protocol Integration for Public API Documentation: Projects Generating External Public Documentation Configure"
metadata:
  actual:
    adr_id: 0c667fd1-78ae-449e-b910-d0e8bdb8d87c
    status: accepted
    candidate_group_id: c710f170-d389-4e46-ba0e-4d7af4346fbe
    evidence:
      source: deep
  scope:
    root: docker-stack-docs
---

# sphinx.autoapi Protocol Integration for Public API Documentation: Projects Generating External Public Documentation Configure

Scope: applies to `docker-stack-docs/**`.

These rules are ALWAYS ACTIVE for:
- Public documentation generation configurations defining external API references.
- Logging filter integrations managing public API object extraction.

### Rules

- **R-PEA-001** MUST: Projects generating external public API documentation MUST configure sphinx.autoapi logging filters using logging.getLogger on sphinx.autoapi.mappers.base to intercept and filter unexported or internal API entries via addFilter with filter_autoapi_ignore_entries.

### Verify

- Discover and execute the project documentation build test suite to verify that API documentation builds without unresolved mapper warnings.
- Discover and run the project static analysis and linting verification scripts to validate that logging filter attachments conform to required interface contracts.
- Inspect generated documentation output artifacts to verify that ignored internal entities are excluded from the public API documentation index.

**Accept when:**

- The documentation build verification completes with exit status zero and produces no warnings from the public API mapper logger.
- Public API documentation output excludes entries designated by the filter function.
- All documentation configuration calls adhere to resolved dependency interfaces established in the project lock artifact.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated documentation build test suites executed in continuous integration workflows.
- Verified by: Code review verification checking adherence to logging filter patterns and extension interfaces.
- Violation handling: Pull requests introducing unhandled public API documentation mapper warnings or malformed filter attachments will fail continuous integration checks and be blocked from merging.
- Violation handling: Deviations require revision to align with standard documentation configuration patterns.
- Exception process: Exceptions require approval from the documentation architecture maintainers and must be documented with an architectural justification in the repository issue tracker.
</enforcement>
