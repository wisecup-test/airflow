---
version: 2
name: 5bdc4d39-faef-4e0b-98e5-4aae5a9badaf-adoption-of-airflow-shared-secrets-masker-for-centralized-secret-redaction-model-response-definitions-handling-unstructured-nested
description: "Adoption of airflow._shared.secrets_masker for Centralized Secret Redaction: Model Response Definitions Handling Unstructured Nested"
metadata:
  actual:
    adr_id: 5bdc4d39-faef-4e0b-98e5-4aae5a9badaf
    status: accepted
    candidate_group_id: 0c1cc8ab-f614-448b-8d9a-f20496e63e77
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of airflow._shared.secrets_masker for Centralized Secret Redaction: Model Response Definitions Handling Unstructured Nested

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- API endpoints and serialization pipelines process configuration attributes, connection parameters, and arbitrary key-value variables that frequently contain sensitive credentials and tokens.
- Exposing raw payloads in response serializers or serialized template outputs creates critical data leakage vulnerabilities across client interfaces and logs.
- A common internal library module, airflow._shared.secrets_masker, provides centralized redaction logic to ensure secrets are consistently masked prior to transmission or serialization.

## Problem Statement

Distributed endpoints and serialization routines handling user-defined configurations, variables, and connection metadata risk exposing credentials or sensitive tokens unless a consistent, centralized redaction mechanism is applied across all API data models and serialization helpers.

## Decision

- **R-LM-001** SHOULD: Model response definitions handling unstructured or nested dictionary attributes SHOULD integrate the redact function inside validator hooks executed after initial model validation.

## Policy Block

In scope:

- API response schemas exposing entity metadata, extra configurations, and environment or variable values
- Core serialization helpers handling dictionary normalization and template field serialization
- Public API service operations performing bulk extraction or modification of connection credentials

Out of scope:

- Internal worker execution contexts requiring unredacted credentials to establish active third-party connections
- Non-sensitive operational metrics, telemetry counts, and scalar system identifiers

Exceptions:

- **EXC-001** Direct execution environments where connection drivers require raw credential strings to authenticate against external services
  Approval: Security lead approval
  Documentation: Document authenticated bypass in the service integration specification

## Rationale

- Adopting airflow._shared.secrets_masker establishes a single source of truth for secret detection and redaction rules, preventing disparate masking behaviors across endpoints.
- Executing redaction within post-validation hooks and serialization helpers ensures that incoming and outgoing payloads are sanitized before leaving service boundaries.
- Centralizing the redaction logic minimizes code duplication and guarantees uniform security enforcement when new secret patterns are introduced.

## Consequences

Positive:

- Eliminates credential leakage across API response models, serialization outputs, and public service layers.
- Standardizes redaction behavior across diverse data structures including nested dictionaries and serialized strings.
- Decouples secret masking logic from endpoint handlers, simplifying maintenance and security audits.

Negative:

- Introduces computational overhead during serialization due to recursive data inspection and pattern matching.
- Requires careful handling of serialized strings versus structured objects within validator functions to avoid serialization errors.

## Alternatives

- Field-level custom regex redaction in individual data models and services (rejected)
  Rejected because: Decentralized redaction patterns create inconsistent masking coverage, risk regex divergence across endpoints, and increase maintenance overhead.
  When valid: Valid only in standalone, isolated utilities that cannot take an internal dependency on shared project modules.
- Database-level masking on read queries (rejected)
  Rejected because: Masking at the persistence boundary obscures plaintext credentials needed by internal task runners and external connectors, breaking core operational workflows.
  When valid: Valid when data is purely archived or exported for analytics where plaintext recovery is strictly prohibited.

## Risks

- Performance degradation during high-throughput serialization due to recursive dictionary traversal and string matching in redaction routines.
  Mitigation: Limit deep redaction to fields designated as containing arbitrary or sensitive metadata, avoiding redundant passes over verified scalar fields.
  Owner: Core engineering team
- Accidental double-serialization or serialization failure when stringified payloads cannot be parsed as structured data.
  Mitigation: Wrap parsing attempts in structured fallback blocks that pass the raw string with its associated field key to the redaction function.
  Owner: Core engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- When integrating redaction into response validation hooks, check whether payload values represent serialized string representations or parsed data structures and handle decoding branches accordingly before passing keys and values to the redact function.
- Apply masking at the boundary layer directly within model validators or service mutation layers to prevent unredacted values from escaping into logging pipelines or external client responses.

## Continuation Context

Verify commands:

- Discover the test runner from the project manifest and execute the test suite targeting data model validation and secret masking routines.
- Discover and run the project static analysis and linting scripts to verify compliance with shared masking import constraints.

Accept when:

- All automated test suites for API response models and serialization helpers pass without exposing unredacted sensitive values.
- Static analysis validates that all sensitive field serialization paths import and execute the centralized masking function.

## Enforcement

- Verified by: Automated test suites validating payload redaction in API response models and serialization outputs.
- Verified by: Static analysis and peer code reviews ensuring all newly created response models and serialization routines adopt the shared masker.
- Violation handling: Pull requests exposing unredacted sensitive fields or bypassing the shared masking module are blocked in code review and continuous integration checks.
- Exception process: Exceptions must be submitted via an architecture review request detailing why raw credentials must be transmitted, subject to explicit security lead approval.
