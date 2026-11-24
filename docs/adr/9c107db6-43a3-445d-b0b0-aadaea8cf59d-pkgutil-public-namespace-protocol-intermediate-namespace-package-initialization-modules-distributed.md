---
version: 2
name: 9c107db6-43a3-445d-b0b0-aadaea8cf59d-pkgutil-public-namespace-protocol-intermediate-namespace-package-initialization-modules-distributed
description: "pkgutil Public Namespace Protocol: Intermediate Namespace Package Initialization Modules Distributed"
metadata:
  actual:
    adr_id: 9c107db6-43a3-445d-b0b0-aadaea8cf59d
    status: accepted
    candidate_group_id: ca65f566-bc3e-4a51-b004-c1ca2bef6bd1
    evidence:
      source: deep
  scope:
    root: providers/apache/flink
    paths:
      - src/airflow/**
---

# pkgutil Public Namespace Protocol: Intermediate Namespace Package Initialization Modules Distributed

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `providers/apache/flink`

Applies to:
- `src/airflow/**`

## Context

- The codebase organizes modular features and external integrations into distributed provider packages that share common top-level namespace hierarchies.
- Independent distribution packages sharing the same root module namespace require an explicit mechanism to prevent module masking and namespace collision at runtime.
- Standardizing on the `pkgutil` protocol via `__import__("pkgutil")` enables intermediate package levels to extend path resolution dynamically across separate installation locations.

## Problem Statement

When a system distributes extensions and provider modules across separate packages sharing a common root namespace, static directory structures can cause earlier-loaded packages to shadow or block access to sibling modules. A unified, standardized namespace protocol is required across all intermediate package layers to dynamically discover and merge split module directories into a single coherent public namespace without loading domain dependencies prematurely.

## Decision

- **R-PEA-001** MUST: Intermediate namespace package initialization modules in distributed packages MUST invoke `__import__("pkgutil")` to establish public namespace traversal protocols across independently installed distributions.

## Policy Block

In scope:

- Intermediate package initialization modules across distributed provider packages that share common root namespace paths
- Public API boundary modules establishing multi-distribution namespace integration

Out of scope:

- Leaf implementation modules containing concrete business logic and integration code
- Self-contained standalone packages that do not share root namespace hierarchies with other distributions

## Rationale

- Invoking `__import__("pkgutil")` ensures that runtime path resolution aggregates modules across distinct distribution directories sharing the same namespace prefix.
- Executing the protocol dynamically at the initialization level preserves independent package installation while exposing a continuous public API boundary.
- Consistent protocol declaration prevents namespace masking where one installed provider would otherwise conceal sibling packages sharing parent namespace paths.

## Consequences

Positive:

- Enables decoupled packaging and independent distribution releases under a unified top-level import namespace.
- Prevents module shadowing across independently installed provider extensions.
- Maintains backward-compatible public import paths for external consumers regardless of which provider distributions are installed.

Negative:

- Requires strict protocol uniformity across every intermediate package initialization module in every distributed subproject.
- Introduces dynamic path inspection overhead during initial package resolution.

## Alternatives

- Native implicit namespace packages without package initialization modules (rejected)
  Rejected because: Omitting initialization files across legacy environments can cause inconsistent module discovery across different installation modes and packaging configurations.
  When valid: When all consumer environments strictly enforce modern packaging standards that uniformly support implicit namespaces without legacy distribution requirements.
- Monolithic single-package distribution containing all provider integrations (rejected)
  Rejected because: Forces all external dependencies and provider integrations into a single large distribution, increasing bundle size and dependency conflict potential.
  When valid: When all extensions share identical deployment cycles, runtime environments, and minimal dependency requirements.

## Risks

- An intermediate namespace directory omits the `__import__("pkgutil")` protocol, breaking namespace traversal for sibling packages.
  Mitigation: Automate structural validation in continuous integration to verify that every intermediate package level includes the required protocol invocation.
  Owner: engineering team
- Side effects or eager submodule imports placed inside namespace initialization modules cause import cycles or performance degradation.
  Mitigation: Enforce static analysis rules disallowing eager domain imports in namespace initialization modules.
  Owner: engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure the `__import__("pkgutil")` namespace declaration is executed immediately upon namespace module evaluation without preceding heavy imports.
- Apply the namespace protocol identically at each intermediate package directory level between the distribution root and specific provider feature modules.

## Continuation Context

Verify commands:

- Discover the project verification script from the repository manifest and execute the namespace structural lint suite.
- Execute repository test runners to confirm all distributed submodules resolve under the shared parent namespace.

Accept when:

- All intermediate namespace initialization modules contain the required `__import__("pkgutil")` protocol statement.
- Distributed provider modules import successfully under the shared namespace hierarchy without module shadowing.

## Enforcement

- Verified by: Automated structural checks in continuous integration pipelines validating namespace initialization declarations
- Verified by: Pull request review verifying new provider distributions follow namespace packaging conventions
- Violation handling: Pull requests missing the required namespace protocol will fail automated verification and require remediation before merge
- Exception process: Exceptions must be documented and reviewed by the technical steering committee for standalone or non-distributed modules
