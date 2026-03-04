# References

## Companion Standards

### PNGE — Protocol-Native Governance Execution

PNGE defines a governance execution pattern where protocols (human-readable documents) define all authority, and code may only implement protocol-defined boundaries. DSI provides the quantitative scoring layer within a PNGE-conformant system.

- Repository: https://github.com/dgp-standard/pnge
- Relationship to DSI: DSI scores measure divergence from PNGE-defined protocols

### SSI — Sovereign Synthetic Intelligence Protocol

SSI defines cryptographic audit provenance: hash-chained decision records, append-only logs, fail-closed semantics, and human authority preservation. DSI evaluation outputs integrate as the scored payload within SSI audit records.

- Repository: https://github.com/dgp-standard/ssi
- Relationship to DSI: SSI provides the immutable audit layer for DSI decisions

## Architectural Precedents

### CVSS — Common Vulnerability Scoring System

CVSS provides an open methodology for scoring security vulnerabilities. DSI follows a similar separation: the scoring methodology is open, while detection and enforcement are implementation-dependent.

- Specification: https://www.first.org/cvss/v4.0/specification-document

### OAuth 2.0

OAuth defines an open authorization framework. Identity providers implement the protocol independently. Similarly, DSI defines governance scoring that any governance engine can implement.

- Specification: https://datatracker.ietf.org/doc/html/rfc6749

## Normative References

- [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt) — Key Words for Use in RFCs to Indicate Requirement Levels
- [Semantic Versioning 2.0.0](https://semver.org/) — Version numbering standard
- [JSON Schema Draft 2020-12](https://json-schema.org/draft/2020-12/schema) — Schema definition language

---

**Copyright 2026 DeAlgo Foundation. Licensed under Apache License 2.0.**
