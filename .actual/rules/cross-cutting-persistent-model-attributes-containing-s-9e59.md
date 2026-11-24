---
version: 2
name: cross-cutting-persistent-model-attributes-containing-s-9e59
description: "Fernet Symmetric Encryption for Persistent Credential Protection: Persistent Model Attributes Containing Sensitive Credentials"
metadata:
  actual:
    adr_id: 9e5986a2-3f1b-49d1-ad42-d6d7814b33e3
    status: accepted
    candidate_group_id: d4e4bda5-2e51-41f3-a64c-05cd7ad18930
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Fernet Symmetric Encryption for Persistent Credential Protection: Persistent Model Attributes Containing Sensitive Credentials

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Persistent data models and database schemas storing connection secrets, passwords, or sensitive credentials.
- Data access operations invoking encryption, decryption, or credential rotation.

### Rules

- **R-DP-001** MUST: Persistent model attributes containing sensitive credentials MUST be encrypted at rest using Fernet symmetric encryption governed by FernetProtocol.

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
