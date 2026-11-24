---
version: 2
name: cross-cutting-key-rotation-routines-execute-through-218c
description: "Fernet Symmetric Encryption for Persistent Credential Protection: Key Rotation Routines Execute Through Rotate"
metadata:
  actual:
    adr_id: 218cca52-a806-45cf-bcf6-cf949075c207
    status: accepted
    candidate_group_id: d4e4bda5-2e51-41f3-a64c-05cd7ad18930
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Fernet Symmetric Encryption for Persistent Credential Protection: Key Rotation Routines Execute Through Rotate

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Persistent data models and database schemas storing connection secrets, passwords, or sensitive credentials.
- Data access operations invoking encryption, decryption, or credential rotation.

### Rules

- **R-DP-001** SHOULD: Key rotation routines SHOULD execute through the rotate contract to re-encrypt stored secrets under newly configured primary keys without service downtime.

### Verify

- Discover the repository test runner from the project configuration and execute test suites targeting model encryption and decryption protocols.
- Discover and run the project linter and static type checker to verify compliance with FernetProtocol signatures.
- Discover and execute cryptographic validation test cases covering key rotation and configuration loading.

**Accept when:**

- All cryptographic unit tests covering encrypt, decrypt, and rotate operations pass with zero failures.
- Static type analysis confirms complete structural compatibility with FernetProtocol across all credential model accessors.
- Automated tests verify that unencrypted secret strings are never written to persistence layers.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration pipelines running static type analysis on model accessors implementing FernetProtocol.
- Verified by: Automated test suites validating encryption round-trips and key rotation behavior.
- Verified by: Peer code reviews verifying compliance with cipher contracts and confirming absence of unencrypted credential persistence.
- Violation handling: Pull requests persisting unencrypted secrets or bypassing FernetProtocol contracts will be blocked by continuous integration.
- Violation handling: Any unencrypted credentials discovered in persistence schemas require immediate flagging and remediation via key migration routines.
- Exception process: Exceptions for non-sensitive data models must be documented in architecture review requests and approved by the security lead.
</enforcement>
