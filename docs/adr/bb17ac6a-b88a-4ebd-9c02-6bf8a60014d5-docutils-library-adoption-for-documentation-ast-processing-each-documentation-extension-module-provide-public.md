---
version: 2
name: bb17ac6a-b88a-4ebd-9c02-6bf8a60014d5-docutils-library-adoption-for-documentation-ast-processing-each-documentation-extension-module-provide-public
description: "Docutils Library Adoption for Documentation AST Processing: Each Documentation Extension Module Provide Public"
metadata:
  actual:
    adr_id: bb17ac6a-b88a-4ebd-9c02-6bf8a60014d5
    status: accepted
    candidate_group_id: fa807058-4765-4f45-97fb-6f3c610aa62f
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
---

# Docutils Library Adoption for Documentation AST Processing: Each Documentation Extension Module Provide Public

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`

## Context

- Documentation builds require custom preprocessing capabilities including placeholder variable replacement within code blocks and automated removal of documentation markers.
- Manipulating raw source text or post-rendered markup leads to syntax highlighter failures, broken source references, and fragile string parsing.
- The docutils library provides structured abstract syntax tree nodes, parser roles, and document traversal mechanisms integrated into SphinxTransform extension lifecycles.

## Problem Statement

Documentation code samples require dynamic variable substitution and automated removal of tutorial markers, but modifying text outside the document abstract syntax tree disrupts syntax highlighting, corrupts nested nodes, and fails across diverse documentation output formats.

## Decision

- **R-LM-001** MUST: Each documentation extension module MUST provide a public setup callable function to register transforms, directives, and roles into the documentation processing environment.

## Policy Block

In scope:

- Documentation build extensions, custom roles, custom directives, and abstract syntax tree transformations.
- Preprocessing and postprocessing steps operating on documentation markup and code block nodes.

Out of scope:

- Standard documentation authoring utilizing existing built-in markup features without custom extensions.
- Application runtime code outside the documentation compilation toolchain.

## Rationale

- Adopting docutils abstract syntax tree transformations guarantees that node mutations respect document hierarchy and preserve parser guarantees across all output builders.
- Extending SphinxTransform allows precise scheduling via default_priority, ensuring text substitutions occur prior to syntax highlighting passes.
- Centralizing custom roles and directives through docutils parsers ensures consistent error handling and seamless integration with the document processing pipeline.

## Consequences

Positive:

- Provides robust, structured document manipulation that preserves document hierarchy and inline element relationships.
- Executes text substitutions and marker cleanup prior to syntax highlighting, ensuring clean code blocks and correct token styling.
- Maintains cross-format compatibility by operating at the abstract syntax tree level rather than on target-specific markup.

Negative:

- Couples documentation preprocessors directly to docutils internal abstract syntax tree representations and node hierarchy contracts.
- Requires careful coordination of transform priority integers to avoid race conditions with core highlighting and doctest transforms.

## Alternatives

- Raw regular expression preprocessing on source documentation files prior to parsing (rejected)
  Rejected because: Bypassing the structured document parser fails to handle nested markup, breaks source line mapping, and risks corrupting code examples containing matching tokens.
  When valid: Valid only for lightweight standalone scripts operating entirely outside the documentation build toolchain.
- Post-rendering markup string manipulation on compiled HTML artifacts (rejected)
  Rejected because: Post-rendering string replacement occurs after syntax highlighting has already executed, does not support non-HTML compilation targets, and introduces fragile string parsing.
  When valid: Valid only when injecting external browser assets into finalized web pages.

## Risks

- Upstream updates to docutils node schemas or transform priority values could alter execution order or break document traversal.
  Mitigation: Lock exact dependency versions in repository lock artifacts and verify transform outputs using continuous integration documentation compilation tests.
  Owner: documentation engineering team
- Incomplete synchronization of rawsource during text node replacement causes syntax highlighters to reject blocks or emit unstyled code.
  Mitigation: Enforce strict synchronization of node rawsource whenever node text is altered, backed by automated unit tests.
  Owner: documentation engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Coordinate transform default_priority carefully: transformations replacing placeholders must execute before syntax highlighting transforms, while marker trimming transforms should run after initial code identification passes.
- When substituting text in child nodes, ensure only text node instances are replaced to avoid inadvertently destroying nested inline elements.

## Continuation Context

Verify commands:

- Discover the project documentation build script from repository configuration manifests and execute it to verify that document tree transformations process without raising role or transform exceptions.
- Discover and run the test suite targeting documentation extensions using the repository test runner to validate node substitution and marker trimming behaviors.

Accept when:

- Documentation build completes successfully with all custom roles, substitution blocks, and transformed nodes processed without parser errors or syntax highlighter warnings.
- All automated test suites covering document transform priority ordering and abstract syntax tree mutations pass with zero failures.

## Enforcement

- Verified by: Continuous integration documentation compilation pipelines configured to fail on document parsing warnings or transform exceptions.
- Verified by: Automated unit and integration test suites validating abstract syntax tree node substitutions and marker trimming.
- Verified by: Peer review for changes within documentation extension modules.
- Violation handling: Pull requests that introduce raw string manipulation on documentation sources or fail to synchronize rawsource on transformed nodes are rejected at continuous integration gates.
- Violation handling: Unhandled transform exceptions block documentation deployment until resolved.
- Exception process: Requests for exceptions to bypass docutils abstract syntax tree processing require review and approval by documentation toolchain maintainers with documented architectural justification.
