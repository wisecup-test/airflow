---
version: 2
name: cross-cutting-documentation-preprocessors-custom-direc-f05a
description: "Docutils Library Adoption for Documentation AST Processing: Documentation Preprocessors Custom Directives Roles Adopt"
metadata:
  actual:
    adr_id: f05af231-cecf-4642-8f26-97404c667a52
    status: accepted
    candidate_group_id: fa807058-4765-4f45-97fb-6f3c610aa62f
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
---

# Docutils Library Adoption for Documentation AST Processing: Documentation Preprocessors Custom Directives Roles Adopt

Scope: applies to `devel-common/src/sphinx_exts/**`.

These rules are ALWAYS ACTIVE for:
- Documentation build extensions, custom roles, custom directives, and abstract syntax tree transformations.
- Preprocessing and postprocessing steps operating on documentation markup and code block nodes.

### Rules

- **R-LM-001** MUST: Documentation preprocessors, custom directives, and custom roles MUST adopt docutils as the standard document tree manipulation and parsing framework, executing all document mutations via the docutils abstract syntax tree rather than regex-based string manipulation on rendered outputs.

### Verify

- Discover the project documentation build script from repository configuration manifests and execute it to verify that document tree transformations process without raising role or transform exceptions.
- Discover and run the test suite targeting documentation extensions using the repository test runner to validate node substitution and marker trimming behaviors.

**Accept when:**

- Documentation build completes successfully with all custom roles, substitution blocks, and transformed nodes processed without parser errors or syntax highlighter warnings.
- All automated test suites covering document transform priority ordering and abstract syntax tree mutations pass with zero failures.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration documentation compilation pipelines configured to fail on document parsing warnings or transform exceptions.
- Verified by: Automated unit and integration test suites validating abstract syntax tree node substitutions and marker trimming.
- Verified by: Peer review for changes within documentation extension modules.
- Violation handling: Pull requests that introduce raw string manipulation on documentation sources or fail to synchronize rawsource on transformed nodes are rejected at continuous integration gates.
- Violation handling: Unhandled transform exceptions block documentation deployment until resolved.
- Exception process: Requests for exceptions to bypass docutils abstract syntax tree processing require review and approval by documentation toolchain maintainers with documented architectural justification.
</enforcement>
