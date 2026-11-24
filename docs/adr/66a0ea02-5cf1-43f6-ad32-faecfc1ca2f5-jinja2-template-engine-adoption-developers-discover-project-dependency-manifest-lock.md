---
version: 2
name: 66a0ea02-5cf1-43f6-ad32-faecfc1ca2f5-jinja2-template-engine-adoption-developers-discover-project-dependency-manifest-lock
description: "Jinja2 Template Engine Adoption: Developers Discover Project Dependency Manifest Lock"
metadata:
  actual:
    adr_id: 66a0ea02-5cf1-43f6-ad32-faecfc1ca2f5
    status: accepted
    candidate_group_id: 3f9abf65-5a3d-4617-8145-8fe94f291554
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
---

# Jinja2 Template Engine Adoption: Developers Discover Project Dependency Manifest Lock

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`

## Context

- Documentation tooling requires programmatic generation of complex reference documentation, index structures, and injected search components from source code inspection.
- Embedding markup templates directly into programmatic generation logic creates high coupling between data extraction routines and output formatting.
- The documentation toolchain standardizes on Jinja2 to render structured documentation artifacts and extensions.

## Problem Statement

Documentation generation routines and build extensions require dynamic content synthesis from codebase introspection and metadata catalogs. Performing markup construction via ad-hoc string concatenation or inline formatting tangles document structure with data processing logic, impeding maintainability and increasing the likelihood of syntax formatting defects.

## Decision

- **R-LM-001** MUST: Developers MUST discover the project dependency manifest and lock file to verify the exact resolved version of Jinja2 before calling template APIs.

## Policy Block

In scope:

- Dynamic documentation generation scripts and documentation builder extensions
- Modules rendering structured reference tables, indexes, or HTML components for documentation

Out of scope:

- Core runtime application components outside documentation build extensions
- Static documentation files that require no dynamic programmatic generation

Exceptions:

- **EXC-20-001** Single-line string interpolations or trivial token replacements where template engine overhead is disproportionate
  Approval: Documentation engineering review
  Documentation: Record rationale in module docstring

## Rationale

- Consistent adoption of Jinja2 across documentation extensions ensures a uniform templating paradigm for index generation, reference building, and search injection.
- Extracting presentation layout into Jinja2 templates enforces a clean separation of concerns between source AST analysis and output markup formatting.
- Reusing template environment resolution patterns simplifies maintenance of documentation assets across multi-provider documentation builds.

## Consequences

Positive:

- Decouples data extraction and AST parsing logic from document presentation and layout.
- Enables template reuse and consistent visual styling across disparate documentation generators.
- Simplifies maintenance by centralizing markup structure in dedicated template assets.

Negative:

- Introduces runtime template compilation and rendering overhead during documentation builds.
- Requires developers working on documentation extensions to maintain proficiency in both host language and Jinja2 template syntax.
- Template syntax errors and missing variable bindings are deferred to render time rather than detected at static import time.

## Alternatives

- Native host language string formatting and concatenation (rejected)
  Rejected because: Leads to unmaintainable inline markup, complex escaping requirements, and tight coupling of data processing logic to presentation layout.
  When valid: Trivial single-variable text substitutions in localized utility functions.
- Alternative template engines (rejected)
  Rejected because: Jinja2 is already established across the documentation toolchain, and introducing a secondary engine creates unnecessary dependency divergence and operational complexity.
  When valid: Documentation frameworks with strictly mandated incompatible template formats.

## Risks

- Undefined template variables failing silently or causing build crashes during documentation generation.
  Mitigation: Enforce strict template context validation and integration testing of documentation build routines.
  Owner: Documentation engineering team
- Drift in template loading mechanisms leading to fragmented environment configurations across extensions.
  Mitigation: Standardize environment provider helpers across documentation extension packages.
  Owner: Documentation tooling maintainers

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Isolate template loading through centralized environment accessors to ensure proper search path resolution and caching.
- Store external template files alongside extension packages in dedicated resource directories to preserve relative path discovery.

## Continuation Context

Verify commands:

- Discover and run the repository documentation build verification suite to confirm templates render successfully without syntax or context errors.
- Discover and execute the project linter and test runner targeting documentation extension modules.

Accept when:

- All documentation generation modules render target output files without template syntax or missing variable errors.
- Automated tests for documentation extensions pass verification with full coverage of template rendering paths.

## Enforcement

- Verified by: Automated continuous integration checks running documentation generation suites.
- Verified by: Peer code review for changes to documentation extensions and build scripts.
- Violation handling: Pull requests containing hardcoded HTML or multi-line markup strings in documentation extensions are blocked pending template refactoring.
- Exception process: Exceptions must be documented with an architectural note and approved by documentation maintainers.
