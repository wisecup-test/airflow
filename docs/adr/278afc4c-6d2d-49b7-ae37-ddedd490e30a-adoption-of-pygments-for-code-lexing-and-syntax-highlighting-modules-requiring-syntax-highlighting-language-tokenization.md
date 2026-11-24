---
version: 2
name: 278afc4c-6d2d-49b7-ae37-ddedd490e30a-adoption-of-pygments-for-code-lexing-and-syntax-highlighting-modules-requiring-syntax-highlighting-language-tokenization
description: "Adoption of Pygments for Code Lexing and Syntax Highlighting: Modules Requiring Syntax Highlighting Language Tokenization"
metadata:
  actual:
    adr_id: 278afc4c-6d2d-49b7-ae37-ddedd490e30a
    status: accepted
    candidate_group_id: 756511ac-5058-4160-854d-f6a8b1372024
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/docs_build/**
      - src/tests_common/test_utils/perf/perf_kit/**
---

# Adoption of Pygments for Code Lexing and Syntax Highlighting: Modules Requiring Syntax Highlighting Language Tokenization

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/docs_build/**`
- `src/tests_common/test_utils/perf/perf_kit/**`

## Context

- Developer utilities and documentation build pipelines require automated language detection, code snippet preparation, and syntax-aware formatting for console diagnostics and documentation generation
- Static analysis reveals direct usage of the Pygments library across both database performance tracing instrumentation and documentation build extensions
- Invocations of get_lexer_for_filename and snippet preparation contracts demonstrate reliance on externalized tokenization rather than internal ad-hoc parsing rules

## Problem Statement

Formatting code snippets for documentation and rendering diagnostic query traces in developer tooling requires robust tokenization across varying programming and query languages. Reimplementing custom regular-expression highlighters or language detection rules creates maintenance overhead, parsing errors, and inconsistent presentation across developer tooling and published documentation.

## Decision

- **R-LM-001** MUST: Modules requiring syntax highlighting, language tokenization, or code snippet lexing MUST adopt Pygments as the core syntax processing library

## Policy Block

In scope:

- Developer diagnostic tools and query tracing formatters requiring terminal syntax highlighting
- Documentation build extensions and code snippet preparation utilities requiring language lexing

Out of scope:

- Core runtime execution paths processing production operational workloads where syntax rendering is inapplicable

## Rationale

- Utilizing Pygments provides standardized multi-language tokenization and lexer resolution across diagnostic tools and documentation generation pipelines without custom parsing logic
- Evidence shows established integration across performance tracing output formatting and documentation code snippet helpers
- Externalizing syntax detection to an established library reduces maintenance costs and ensures consistent language identification via standardized lexer resolution contracts

## Consequences

Positive:

- Provides reliable multi-language tokenization and syntax highlighting across diagnostic tools and documentation
- Eliminates redundant custom regular expression parsing logic for source code and query formatting
- Standardizes lexer discovery through established contracts such as get_lexer_for_filename

Negative:

- Introduces an external dependency that must be maintained and tracked in developer and documentation environments
- Adds tokenization processing overhead during large-scale documentation compilation and high-volume trace formatting

## Alternatives

- In-house regular expression syntax formatting utilities (rejected)
  Rejected because: In-house regular expression matchers fail to handle complex multi-token language grammars accurately and create significant ongoing maintenance overhead
  When valid: Valid only in constrained execution environments where external dependencies are strictly prohibited and formatting needs are limited to single-word keywords
- Client-side browser syntax highlighting exclusively during documentation presentation (rejected)
  Rejected because: Client-side rendering does not satisfy the requirement for terminal and log-based syntax formatting within diagnostic tools and build-time snippet validation
  When valid: Valid when syntax highlighting is exclusively restricted to web-rendered documentation without any console or build-time tokenization needs

## Risks

- Pygments version discrepancies causing unexpected lexer behavior or deprecated API usage
  Mitigation: Enforce strict lockfile resolution and verify API availability against exact versioned documentation prior to implementation
  Owner: Engineering Team
- Performance degradation during intensive query tracing due to synchronous syntax highlighting overhead
  Mitigation: Isolate formatting from execution hooks and defer highlighting until output presentation
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- When preparing code snippets from file paths, invoke lexer resolution helpers with fallback handling to accommodate unrecognized file extensions or missing language definitions
- Ensure diagnostic utilities encapsulating query output apply Pygments formatters only when active output channels support styled terminal display

## Continuation Context

Verify commands:

- Discover and execute the project test runner targeting documentation extension test suites and query tracing unit tests
- Execute the repository code quality and static analysis suite to verify interface compliance and dependency import hygiene

Accept when:

- All unit tests covering documentation snippet lexing and diagnostic query tracing pass without import errors or unhandled lexer exceptions
- Pygments API calls conform strictly to the version verified against the project lock resolution artifact

## Enforcement

- Verified by: Continuous integration automated test suites
- Verified by: Peer code review during pull request inspection
- Violation handling: Pull requests introducing unapproved custom tokenizers or unverified library API calls will be blocked from merging until resolved
- Exception process: Exceptions for performance-critical paths or specialized language tokenizers require documented rationale and approval from the technical architecture group
