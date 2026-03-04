# Changelog

All notable changes to the DSI specification will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this specification adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-03-03

### Added

- Initial DSI v1.0 specification (SPEC.md)
- Four-component scoring formula (FORMULA.md)
  - Structural Compliance (C_s)
  - Scope Fidelity (C_f)
  - Confidence Clarity (C_c)
  - Escalation Awareness (C_e)
- Classification taxonomy (TAXONOMY.md)
  - 4 verdict types: APPROVE, DELAY, DENY, ESCALATE
  - 8 action types: READ_FILE, WRITE_FILE, RUN_COMMAND, WEB_REQUEST, BROWSER_CONTROL, PAYMENT, TRANSFER_ASSET, OTHER
  - 3 risk tiers: LOW, MEDIUM, HIGH
  - 3 chain risk patterns: APPROVAL_ACCUMULATION, THRESHOLD_CLUSTERING, AUTHORITY_DILUTION
- JSON Schema definitions for evaluation output and governance capsule
- Illustrative examples (net-new, non-operational)
- Three conformance levels: Basic, Standard, Full
- References to companion standards (PNGE, SSI)
- Apache 2.0 license
