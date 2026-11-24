---
version: 2
name: cross-cutting-provider-implementations-not-alter-publi-6b35
description: "Airflow Provider Contract Interface and Packaging Version Compatibility Pattern: Provider Implementations Not Alter Public Contract"
metadata:
  actual:
    adr_id: 6b35cf8e-6ac8-449f-bd48-8cc1cb58955d
    status: accepted
    candidate_group_id: 595312d9-672a-4216-825d-1b639f88b2e3
    evidence:
      source: deep
  scope:
    root: providers/apache/beam
    paths:
      - src/airflow/providers/apache/beam/**
---

# Airflow Provider Contract Interface and Packaging Version Compatibility Pattern: Provider Implementations Not Alter Public Contract

Scope: applies to `providers/apache/beam/src/airflow/providers/apache/beam/**`.

These rules are ALWAYS ACTIVE for:
- Provider integration packages that expose public contracts and capabilities to the host orchestration platform.
- Provider modules performing runtime platform version negotiation and compatibility checks.

### Rules

- **R-PEA-001** MUST_NOT: Provider implementations MUST_NOT alter public contract return signatures or omit required metadata fields specified by the platform integration contract.

### Verify

- Discover and execute the repository test suite to verify provider contract interfaces and version compatibility routines against all supported environments.
- Discover and execute the metadata validation scripts to confirm that the provider information dictionary satisfies host platform contract expectations.

**Accept when:**

- All provider contract entry points expose compliant metadata structures matching core orchestrator integration requirements.
- Version comparison routines consistently use structured Version objects from the packaging library without runtime type exceptions.
- The project test suite and contract validation scripts pass without compatibility regressions across supported runtime configurations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated contract test suites validating provider discovery metadata return structures.
- Verified by: Continuous integration matrix builds running provider suites across supported core platform environments.
- Verified by: Pull request architecture reviews inspecting public interface implementations and version compatibility logic.
- Violation handling: Pull requests missing the required provider discovery contract or employing non-standard version parsing must fail automated verification.
- Violation handling: Non-compliant version checks must be flagged during static inspection and blocked prior to integration release.
- Exception process: Proposals to introduce alternative contract hooks or versioning mechanisms require review and approval from platform architecture maintainers.
- Exception process: Any temporary exception must be documented in the provider implementation with a tracking ticket and deprecation timeline.
</enforcement>
