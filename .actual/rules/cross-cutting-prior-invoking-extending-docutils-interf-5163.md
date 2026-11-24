---
version: 2
name: cross-cutting-prior-invoking-extending-docutils-interf-5163
description: "Docutils Library Adoption for Documentation AST Processing: Prior Invoking Extending Docutils Interfaces Consumer"
metadata:
  actual:
    adr_id: 5163833b-a2ff-4f88-9e2d-0751047bfd1d
    status: accepted
    candidate_group_id: fa807058-4765-4f45-97fb-6f3c610aa62f
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
---

# Docutils Library Adoption for Documentation AST Processing: Prior Invoking Extending Docutils Interfaces Consumer

Scope: applies to `devel-common/src/sphinx_exts/**`.

These rules are ALWAYS ACTIVE for:
- Documentation build extensions, custom roles, custom directives, and abstract syntax tree transformations.
- Preprocessing and postprocessing steps operating on documentation markup and code block nodes.

### Rules

- **R-LM-001** MUST: Prior to invoking or extending docutils interfaces, the consumer MUST inspect the repository dependency manifest, resolve the exact version from the repository lock artifact, and confirm that all referenced node classes, parser contracts, and traversal APIs are present in that resolved version.

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
