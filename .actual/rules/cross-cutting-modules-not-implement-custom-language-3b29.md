---
version: 2
name: cross-cutting-modules-not-implement-custom-language-3b29
description: "Adoption of Pygments for Code Lexing and Syntax Highlighting: Modules Not Implement Custom Language Tokenizers"
metadata:
  actual:
    adr_id: 3b29023b-bc8b-47bc-abb2-fc19400639c1
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

# Adoption of Pygments for Code Lexing and Syntax Highlighting: Modules Not Implement Custom Language Tokenizers

Scope: applies to `devel-common/src/sphinx_exts/docs_build/**`, `devel-common/src/tests_common/test_utils/perf/perf_kit/**`.

These rules are ALWAYS ACTIVE for:
- Developer diagnostic tools and query tracing formatters requiring terminal syntax highlighting
- Documentation build extensions and code snippet preparation utilities requiring language lexing

### Rules

- **R-LM-001** MUST_NOT: Modules MUST NOT implement custom language tokenizers or ad-hoc regular expression highlighters for languages supported by the adopted syntax library

### Verify

- Discover and execute the project test runner targeting documentation extension test suites and query tracing unit tests
- Execute the repository code quality and static analysis suite to verify interface compliance and dependency import hygiene

**Accept when:**

- All unit tests covering documentation snippet lexing and diagnostic query tracing pass without import errors or unhandled lexer exceptions
- Pygments API calls conform strictly to the version verified against the project lock resolution artifact

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration automated test suites
- Verified by: Peer code review during pull request inspection
- Violation handling: Pull requests introducing unapproved custom tokenizers or unverified library API calls will be blocked from merging until resolved
- Exception process: Exceptions for performance-critical paths or specialized language tokenizers require documented rationale and approval from the technical architecture group
</enforcement>
