---
version: 2
name: cross-cutting-modules-requiring-syntax-highlighting-la-278a
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

Scope: applies to `devel-common/src/sphinx_exts/docs_build/**`, `devel-common/src/tests_common/test_utils/perf/perf_kit/**`.

These rules are ALWAYS ACTIVE for:
- Developer diagnostic tools and query tracing formatters requiring terminal syntax highlighting
- Documentation build extensions and code snippet preparation utilities requiring language lexing

### Rules

- **R-LM-001** MUST: Modules requiring syntax highlighting, language tokenization, or code snippet lexing MUST adopt Pygments as the core syntax processing library

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
