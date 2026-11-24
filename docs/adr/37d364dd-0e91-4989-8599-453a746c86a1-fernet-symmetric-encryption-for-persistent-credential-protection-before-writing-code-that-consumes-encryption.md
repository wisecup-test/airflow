---
version: 2
name: 37d364dd-0e91-4989-8599-453a746c86a1-fernet-symmetric-encryption-for-persistent-credential-protection-before-writing-code-that-consumes-encryption
description: "Fernet Symmetric Encryption for Persistent Credential Protection: Before Writing Code That Consumes Encryption"
metadata:
  actual:
    adr_id: 37d364dd-0e91-4989-8599-453a746c86a1
    status: accepted
    candidate_group_id: d4e4bda5-2e51-41f3-a64c-05cd7ad18930
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Fernet Symmetric Encryption for Persistent Credential Protection: Before Writing Code That Consumes Encryption

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Persistent data models store sensitive connection credentials and passwords that require encryption at rest to prevent unauthorized exposure.
- Cryptographic key access must decouple persistence schemas from underlying key material through centralized configuration parameters.
- Type safety and formal protocol conformance are required to prevent type confusion errors between byte sequences and text strings during cipher operations.
- Key lifecycle management necessitates an established rotation contract to update ciphertexts as encryption keys are rolled across environments.

## Problem Statement

Storing plaintext connection credentials and passwords in persistent storage exposes sensitive systems to unauthorized data exposure upon database compromise. Without a standardized symmetric encryption protocol, disparate components risk implementing ad-hoc cipher mechanisms, improper key handling, or failing to support zero-downtime key rotation.

## Decision

- **R-DP-001** MUST: Before writing code that consumes the encryption dependency, developers MUST inspect the repository lock or resolution artifact to determine the exact resolved dependency version and confirm API compatibility.

## Policy Block

In scope:

- Persistent data models and database schemas storing connection secrets, passwords, or sensitive credentials.
- Data access operations invoking encryption, decryption, or credential rotation.

Out of scope:

- Transient, in-memory execution tokens and public API authentication endpoints not persisted to relational storage.
- Non-sensitive operational metadata, execution statuses, and configuration parameters that do not contain credentials.

## Rationale

- Fernet provides authenticated symmetric encryption guaranteeing that ciphertext cannot be inspected or altered without access to the secret key.
- Defining a formal FernetProtocol establishes type-safe contracts for encrypt, decrypt, and rotate methods across data access layers.
- Centralizing key resolution to the FERNET_KEY configuration parameter prevents hardcoded secrets and enables consistent rotation across deployments.

## Consequences

Positive:

- Guarantees confidentiality and integrity of stored credentials at rest through authenticated symmetric cryptography.
- Standardizes secret management across models via the explicit FernetProtocol typing interface.
- Facilitates zero-downtime key rotation through the rotate contract without schema migrations.

Negative:

- Introduces computational overhead during persistence read and write cycles for cryptographic transformations.
- Increases operational complexity for managing key distribution, secret rotation, and environment synchronization for FERNET_KEY.
- Misconfiguration or unrecoverable loss of the decryption key causes irreversible data loss for encrypted credentials.

## Alternatives

- Database-level transparent data encryption without application cipher contracts (rejected)
  Rejected because: Database-level encryption protects storage volumes but leaves sensitive credentials exposed to unauthenticated database clients and application-level memory leaks.
  When valid: Valid when database storage compliance is enforced purely at infrastructure levels without requiring application-managed key segregation.
- Direct unabstracted cipher calls without typing protocol abstraction (rejected)
  Rejected because: Omitting a formal protocol increases type confusion between string and byte representations and impedes automated contract verification.
  When valid: Valid in ephemeral administrative scripts where typing overhead outweighs security consistency benefits.

## Risks

- Loss or corruption of the configured FERNET_KEY causes permanent data loss for all encrypted credentials.
  Mitigation: Enforce secure multi-environment secret backups and configuration validation upon service initialization.
  Owner: Data Platform Engineering Team
- Type mismatch between string and byte payloads during cipher operations triggers unhandled runtime exceptions.
  Mitigation: Validate payload types at protocol boundaries and assert explicit type checks before invoking cipher operations.
  Owner: Core Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure that Fernet keys are verified as URL-safe base64-encoded strings prior to cipher object instantiation.
- Implement rotation procedures by configuring multi-key Fernet instances where the primary key encrypts and secondary keys decrypt legacy ciphertexts.

## Continuation Context

Verify commands:

- Discover the repository test runner from the project configuration and execute test suites targeting model encryption and decryption protocols.
- Discover and run the project linter and static type checker to verify compliance with FernetProtocol signatures.
- Discover and execute cryptographic validation test cases covering key rotation and configuration loading.

Accept when:

- All cryptographic unit tests covering encrypt, decrypt, and rotate operations pass with zero failures.
- Static type analysis confirms complete structural compatibility with FernetProtocol across all credential model accessors.
- Automated tests verify that unencrypted secret strings are never written to persistence layers.

## Enforcement

- Verified by: Continuous integration pipelines running static type analysis on model accessors implementing FernetProtocol.
- Verified by: Automated test suites validating encryption round-trips and key rotation behavior.
- Verified by: Peer code reviews verifying compliance with cipher contracts and confirming absence of unencrypted credential persistence.
- Violation handling: Pull requests persisting unencrypted secrets or bypassing FernetProtocol contracts will be blocked by continuous integration.
- Violation handling: Any unencrypted credentials discovered in persistence schemas require immediate flagging and remediation via key migration routines.
- Exception process: Exceptions for non-sensitive data models must be documented in architecture review requests and approved by the security lead.
