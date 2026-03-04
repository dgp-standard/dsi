# Divergence Severity Index (DSI) v1.0 Specification

**Version:** 1.0.0
**Status:** Draft Standard
**Date:** 2026-03-03
**Authority:** DeAlgo Foundation
**License:** Apache 2.0 (see LICENSE.md)

---

## Abstract

The Divergence Severity Index (DSI) is an open standard for measuring the degree to which an autonomous agent's proposed action diverges from its declared governance policy.

DSI produces a normalized score (0-100) that quantifies governance alignment across four independent evaluation dimensions. The score, combined with a structured verdict taxonomy, enables deterministic, auditable governance decisions without requiring human review of every agent action.

DSI is designed to be:

- **Vendor-neutral** — Any governance system can implement DSI scoring
- **Deterministic** — Identical inputs MUST produce identical scores
- **Auditable** — Every score is decomposable into its component evaluations
- **Composable** — DSI integrates with audit chain protocols (e.g., SSI) and governance execution patterns (e.g., PNGE)

This specification defines the scoring model, verdict taxonomy, and conformance requirements. It does NOT define enforcement mechanisms, detection heuristics, or execution logic, which are implementation-dependent.

---

## 1. Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).

| Term | Definition |
|------|-----------|
| **Agent** | An autonomous system that proposes actions to be governed |
| **Action** | A discrete operation an agent intends to execute |
| **Intent** | A declarative statement of what the agent proposes to do |
| **Governance Policy** | A set of rules, constraints, and authority boundaries that govern agent behavior |
| **Capsule** | A versioned, portable governance policy configuration |
| **Divergence** | The degree to which a proposed action deviates from the governing policy |
| **DSI Score** | A normalized integer (0-100) representing governance alignment |
| **Verdict** | A categorical governance decision derived from the DSI score and contextual signals |
| **Component Score** | A normalized integer (0-100) representing alignment on a single evaluation dimension |
| **Chain Risk** | Cross-decision behavioral patterns detected over a time window |
| **Founder** | The human authority with ultimate override power in a governed system |
| **Escalation** | The act of deferring a governance decision to a higher authority |

---

## 2. Scope

### 2.1 In Scope

This specification defines:

- The DSI scoring formula and its four component dimensions
- The verdict taxonomy (four verdict types)
- The action classification schema (eight action types)
- The risk tier model (three tiers)
- Chain risk pattern definitions (three behavioral patterns)
- The governance capsule interface contract
- Conformance requirements for implementations
- Security considerations for standard adopters

### 2.2 Out of Scope

This specification does NOT define:

- Action inference heuristics (how intent maps to action types)
- Detection keyword lists or pattern-matching logic
- Enforcement mechanisms or execution gating
- Audit chain implementation (see SSI Protocol)
- Governance execution patterns (see PNGE Standard)
- Specific threshold values for chain risk detection
- Internal scoring penalties or cap values
- Founder authentication or override protocols

Implementers MUST define these elements independently. The boundary between standard and implementation is intentional and MUST be preserved.

---

## 3. Architecture Overview

DSI operates as the **scoring layer** in a governance stack:

```
┌─────────────────────────────────────────────┐
│          Governance Execution Pattern        │
│               (e.g., PNGE)                  │
│                                             │
│  Defines: authority hierarchy, refusal      │
│  semantics, protocol-native execution       │
├─────────────────────────────────────────────┤
│        Divergence Severity Index (DSI)      │  <── THIS SPECIFICATION
│                                             │
│  Defines: scoring formula, verdicts,        │
│  action taxonomy, risk model,               │
│  chain risk patterns                        │
├─────────────────────────────────────────────┤
│           Audit Immutability Layer          │
│               (e.g., SSI)                   │
│                                             │
│  Defines: hash-chained records,             │
│  cryptographic provenance, tamper           │
│  detection, append-only logs                │
└─────────────────────────────────────────────┘
```

These three layers are **independent and composable**. An implementation MAY adopt DSI without SSI or PNGE, though the combination provides the strongest governance guarantees.

### 3.1 Evaluation Pipeline

A DSI-conformant system MUST evaluate agent actions through these logical stages:

1. **Classification** — Infer action types from agent intent
2. **Reflection** — Analyze the action across weighted governance dimensions
3. **Scoring** — Compute the DSI score from component evaluations
4. **Gating** — Map the score and contextual signals to a verdict
5. **Authority** — Apply founder oversight and escalation rules

Implementations MAY name these stages differently. The logical sequence MUST be preserved. Each stage MUST produce traceable output that can be included in an audit record.

---

## 4. DSI Score

### 4.1 Definition

The DSI Score is a normalized integer in the range [0, 100] representing the degree of governance alignment for a proposed agent action.

- **100** = Perfect alignment. No divergence detected.
- **High range** = Aligned. Action conforms to governance policy.
- **Mid range** = Uncertain. Action shows partial divergence requiring review.
- **Low range** = Divergent. Action violates governance policy.

Higher scores indicate greater alignment. Lower scores indicate greater divergence. The specific score ranges that map to each category are deployment-defined (see Section 5.2).

### 4.2 Composition

The DSI Score is computed from four independent component scores, each evaluating a distinct dimension of governance alignment:

| Component | Symbol | Evaluates |
|-----------|--------|-----------|
| **Structural Compliance** | C_s | Whether the action's output contains required governance artifacts (headers, documentation, rollback plans) |
| **Scope Fidelity** | C_f | Whether the action's output stays within the declared task scope, measured by lexical divergence |
| **Confidence Clarity** | C_c | Whether the action's output expresses clear conviction vs. uncertainty or placeholder language |
| **Escalation Awareness** | C_e | Whether the action correctly identifies and signals situations requiring higher authority |

Each component score is an integer in [0, 100].

### 4.3 Formula

The DSI Score is a weighted sum of its four components:

```
DSI = round( w_s * C_s + w_f * C_f + w_c * C_c + w_e * C_e )
```

Where:

- `w_s`, `w_f`, `w_c`, `w_e` are non-negative weights that MUST sum to 1.0
- `round()` is defined as `floor(value + 0.5)` (round half up)
- Each `C_*` is an integer in [0, 100]
- The result MUST be clamped to [0, 100]

### 4.4 Reference Weights

The following reference weights are provided as illustrative defaults for initial deployments:

| Component | Reference Weight |
|-----------|-----------------|
| Structural Compliance (w_s) | 0.25 |
| Scope Fidelity (w_f) | 0.30 |
| Confidence Clarity (w_c) | 0.20 |
| Escalation Awareness (w_e) | 0.25 |

> **IMPORTANT:** Reference weights are demonstrative, not authoritative. Deployments MUST tune weights to their governance environment, risk tolerance, and regulatory requirements. Using reference weights without environment-specific calibration is NOT RECOMMENDED for production systems.

Implementations MUST validate that custom weights sum to 1.0. If custom weights are used, they MUST be recorded in the audit trail alongside the score.

### 4.5 Score Modification

Implementations MAY apply score modifications based on policy violations detected during evaluation. Score modifications MUST:

- Only reduce the score (never increase it)
- Be deterministic for identical inputs
- Be recorded in the evaluation output with a violation code and severity level
- Follow a fail-closed principle: if modification logic errors, the score MUST be reduced, not preserved

The specific modification rules (cap values, penalty amounts) are implementation-defined and SHOULD be configurable through the governance capsule.

### 4.6 Determinism Requirement

A DSI-conformant implementation MUST be deterministic:

- Given identical inputs (intent, governance capsule, evaluation context), the output score MUST be identical
- Implementations MUST NOT introduce randomness, sampling, or probabilistic elements into score computation
- Floating-point arithmetic MUST use the rounding rule defined in Section 4.3
- Score computation MUST NOT depend on wall-clock time, system load, or external state (chain risk evaluation is scoped separately; see Section 8)

---

## 5. Verdict Taxonomy

### 5.1 Verdict Types

Every DSI evaluation MUST produce exactly one verdict from the following set:

| Verdict | Meaning | Typical Score Range |
|---------|---------|-------------------|
| **APPROVE** | Action is aligned with governance policy and may proceed | Score at or above the approval threshold |
| **DELAY** | Action shows partial divergence; hold for review or cooling period | Score between delay and approval thresholds |
| **DENY** | Action violates governance policy and MUST NOT proceed | Score below the delay threshold |
| **ESCALATE** | Action requires higher authority review regardless of score | Triggered by contextual signals |

### 5.2 Verdict Derivation

Verdicts are derived from the DSI score and contextual signals. The derivation follows this precedence:

1. **ESCALATE** — If escalation signals are present AND the action's risk tier requires higher authority review, the verdict MUST be ESCALATE regardless of score
2. **DENY** — If the score is below the delay threshold, the verdict MUST be DENY
3. **DELAY** — If the score is below the approval threshold, the verdict MUST be DELAY
4. **APPROVE** — If the score is at or above the approval threshold AND no escalation signals are triggered, the verdict MAY be APPROVE

Implementations MUST define their own threshold values. The standard does not mandate specific threshold numbers.

### 5.3 Verdict Immutability

Once a verdict is recorded in an audit chain, it MUST NOT be modified. If a governance decision is overridden by a founder or higher authority, the override MUST be recorded as a separate audit entry that references the original verdict. The original verdict record MUST remain intact.

---

## 6. Action Classification

### 6.1 Action Types

DSI defines eight canonical action types that classify the nature of an agent's proposed operation:

| Action Type | Description | Examples |
|-------------|-------------|---------|
| **READ_FILE** | Read data from a file or data store | Open file, query database, read config |
| **WRITE_FILE** | Create, modify, or delete file content | Save file, update record, write log |
| **RUN_COMMAND** | Execute a system command or process | Run script, invoke CLI tool, start service |
| **WEB_REQUEST** | Make an outbound network request | API call, HTTP request, webhook |
| **BROWSER_CONTROL** | Interact with a web browser programmatically | Navigate page, fill form, take screenshot |
| **PAYMENT** | Initiate a monetary transaction | Send payment, create invoice, authorize charge |
| **TRANSFER_ASSET** | Move non-monetary assets between systems | Upload to cloud storage, migrate data, export records |
| **OTHER** | Any action not covered by the above types | Unclassified or novel operations |

### 6.2 Action Inference

An implementation MUST infer one or more action types from the agent's declared intent. The inference mechanism is implementation-defined. The standard requires only that:

- Every evaluated action MUST have at least one action type assigned
- If no type can be inferred, the action MUST be classified as `OTHER`
- The inferred action types MUST be included in the evaluation output
- Multiple action types MAY be assigned to a single intent (e.g., read then write)

### 6.3 Action Type Stability

The eight action types defined in this specification are stable for DSI v1.x. New action types MUST NOT be added in minor versions. Additions require a major version increment (DSI v2.0+).

---

## 7. Risk Model

### 7.1 Risk Tiers

Every action evaluation MUST be assigned a risk tier:

| Tier | Semantic | Characteristics |
|------|----------|----------------|
| **LOW** | Reversible, read-only, or isolated | Action can be undone or has no persistent side effects |
| **MEDIUM** | Persistent but bounded | Action modifies state but within a recoverable scope |
| **HIGH** | Irreversible, financial, or security-sensitive | Action cannot be undone, involves money, or affects security boundaries |

### 7.2 Default Risk Mapping

The following default mapping from action types to risk tiers is provided as a starting point:

| Action Type | Default Risk Tier |
|-------------|------------------|
| READ_FILE | LOW |
| WRITE_FILE | MEDIUM |
| RUN_COMMAND | HIGH |
| WEB_REQUEST | LOW |
| BROWSER_CONTROL | MEDIUM |
| PAYMENT | HIGH |
| TRANSFER_ASSET | HIGH |
| OTHER | LOW |

Implementations SHOULD adjust this mapping based on their environment. The mapping MUST be configurable through the governance capsule.

### 7.3 Risk Elevation

An implementation MAY elevate the risk tier above the default based on contextual analysis. Risk elevation MUST:

- Only increase the tier (never decrease it)
- Be documented in the evaluation output with a reason
- Consider factors such as: destructive intent, security-sensitive targets, irreversibility indicators

The specific elevation heuristics are implementation-defined.

### 7.4 Risk Tier Influence on Verdict

Risk tier interacts with the verdict derivation (Section 5.2):

- HIGH risk actions with escalation signals MUST trigger an ESCALATE verdict even if the DSI score is above the approval threshold
- LOW risk actions SHOULD have a lower effective threshold for APPROVE (implementations MAY adjust)
- Risk tier MUST be recorded in the evaluation output

---

## 8. Chain Risk Patterns

### 8.1 Definition

Chain risk refers to behavioral patterns that emerge across multiple governance decisions over time. A single action may score well in isolation, but a sequence of actions may reveal concerning patterns.

DSI defines three canonical chain risk patterns:

### 8.2 Pattern: Approval Accumulation

**Definition:** A sequence of approved actions within a time window where individual scores cluster above a floor value. Each approval is individually acceptable, but the accumulation of approvals without denial or escalation suggests progressive boundary expansion.

**Signal:** Multiple approvals with moderately elevated scores occurring in rapid succession.

**Severity:** Escalates with count. Implementations MUST define their own count thresholds and time windows.

### 8.3 Pattern: Threshold Clustering

**Definition:** A cluster of actions whose scores fall near the boundary between APPROVE and DELAY. Actions are not failing, but they are consistently near the decision boundary, suggesting the agent is operating at the edge of policy.

**Signal:** Score clustering near the approval threshold within a time window.

**Severity:** Typically MEDIUM. Implementations MUST define their own proximity range and count thresholds.

### 8.4 Pattern: Authority Dilution

**Definition:** A declining rate of escalation over time. Earlier actions in a session triggered appropriate escalations, but later actions with similar risk profiles are being approved without escalation, suggesting progressive erosion of oversight.

**Signal:** Decreasing escalation-to-approval ratio across the evaluation window.

**Severity:** Typically HIGH when detected. Implementations MUST define their own minimum record counts and rate-change thresholds.

### 8.5 Chain Risk Score

When chain risk patterns are detected, implementations MUST compute an aggregate chain risk score in [0.0, 1.0]:

- 0.0 = No chain risk patterns detected
- 1.0 = Maximum chain risk

The chain risk score is NOT a component of the DSI score. It is an independent signal used in verdict derivation (Section 5.2). When chain risk exceeds a policy-defined threshold, it SHOULD trigger escalation regardless of the DSI score.

### 8.6 Chain Risk and Audit

Chain risk evaluation MUST be based solely on the audit history. It MUST NOT modify, delete, or overwrite prior audit records. The chain risk evaluation is a read-only analysis of the decision trail.

---

## 9. Governance Capsule

### 9.1 Definition

A governance capsule is a versioned, portable policy configuration that parameterizes DSI evaluation. Capsules enable different governance policies to be applied to different contexts (agents, environments, compliance regimes) without modifying the scoring engine.

### 9.2 Required Fields

A DSI-conformant capsule MUST include:

```json
{
  "version": "string (semver)",
  "governance": {
    "structuralCompliance": {
      "requiredHeaders": ["array of required governance artifact names"]
    },
    "scopeFidelity": {
      "driftKeywords": ["array of scope-boundary indicator terms"]
    },
    "scoring": {
      "riskThreshold": "LOW | MEDIUM | HIGH"
    },
    "escalation": {
      "triggers": ["array of escalation indicator terms"],
      "requiredForHighRisk": "boolean"
    },
    "chainRisk": {
      "enabled": "boolean",
      "escalationScore": "number (0.0-1.0)",
      "escalationPatterns": ["array of pattern codes"]
    }
  }
}
```

### 9.3 Capsule Versioning

Capsules MUST use semantic versioning (semver). The capsule version MUST be recorded in every audit record produced under that capsule. Changes to a capsule MUST increment the version.

### 9.4 Capsule Composition

When multiple capsules apply to a single evaluation (e.g., an agent governed by both a baseline policy and a compliance overlay), the implementation MUST define merge semantics. The standard RECOMMENDS:

- **Array fields:** Union (combine all entries, deduplicate)
- **Scalar fields:** Last-wins (later capsule overrides earlier)
- **Threshold fields:** Strictest-wins (most restrictive value prevails)
- **Boolean fields:** OR for requirements (any capsule requiring it forces it)

Implementations MUST document their merge semantics.

### 9.5 Capsule Immutability

A capsule version, once published, MUST NOT be modified. To change governance policy, publish a new capsule version. This ensures that audit records referencing a capsule version always reflect the same policy.

---

## 10. Evaluation Output

### 10.1 Required Output Fields

A DSI evaluation MUST produce output containing at minimum:

| Field | Type | Description |
|-------|------|-------------|
| `score` | integer [0-100] | The computed DSI score |
| `verdict` | enum | One of: APPROVE, DELAY, DENY, ESCALATE |
| `components` | object | Individual component scores (C_s, C_f, C_c, C_e) |
| `actionTypes` | array | Inferred action types |
| `riskTier` | enum | LOW, MEDIUM, or HIGH |
| `capsuleVersion` | string | Version of the governing capsule |
| `violations` | array | Policy violations detected (code + severity + message) |
| `chainRisk` | object | Chain risk score and detected patterns (if evaluated) |
| `timestamp` | string (ISO 8601) | Evaluation timestamp in UTC |

### 10.2 Evaluation Determinism

Given identical inputs, the evaluation output (excluding timestamp) MUST be identical. This enables replay verification: an auditor can re-run an evaluation with the same inputs and verify that the same score and verdict are produced.

### 10.3 Audit Integration

The evaluation output SHOULD be recorded in an append-only audit chain. When integrated with the SSI Protocol, the evaluation output becomes part of the cryptographically signed decision record, enabling tamper-evident governance verification.

---

## 11. Conformance

### 11.1 Conformance Levels

| Level | Requirements |
|-------|-------------|
| **DSI Basic** | Implements the scoring formula (Section 4), verdict taxonomy (Section 5), and action classification (Section 6). Produces conformant evaluation output (Section 10). |
| **DSI Standard** | Basic + risk model (Section 7), governance capsules (Section 9), and determinism guarantees (Section 4.6). |
| **DSI Full** | Standard + chain risk evaluation (Section 8) and audit chain integration (Section 10.3). |

### 11.2 Conformance Claims

An implementation claiming DSI conformance MUST:

1. State which conformance level it implements
2. Pass the DSI conformance test suite (when published)
3. Document any extensions or deviations from this specification
4. Not claim conformance for a level whose requirements are partially met

### 11.3 Extensions

Implementations MAY extend DSI with additional component dimensions, verdict types, or action types. Extensions MUST:

- Not conflict with the standard components, verdicts, or action types
- Be clearly documented as extensions
- Use a namespace prefix (e.g., `x-vendor-component`) to avoid collision
- Not be required for conformance

---

## 12. Security Considerations

### 12.1 Adversarial Scoring

Agents operating under DSI governance may attempt to optimize their inputs to achieve higher scores without genuine policy alignment. Implementers SHOULD:

- Use implementation-specific detection heuristics that are NOT derived from this specification
- Treat the published reference weights and threshold concepts as a starting framework, not as operational parameters
- Regularly rotate and tune scoring parameters
- Monitor for score manipulation patterns through chain risk analysis

### 12.2 Threshold Disclosure

This specification intentionally does NOT mandate specific threshold values, penalty constants, or detection heuristics. Implementations MUST define these independently. Publishing operational thresholds enables adversarial optimization and is contrary to the security goals of DSI.

### 12.3 Capsule Integrity

Governance capsules define the policy boundary. If a capsule is tampered with, the governance guarantee is void. Implementations SHOULD:

- Sign capsules cryptographically
- Version capsules immutably
- Record capsule hashes in audit records
- Prevent runtime capsule modification by governed agents

### 12.4 Score Isolation

The DSI score MUST NOT be exposed to the governed agent. An agent that can observe its own DSI score can use that feedback to optimize future actions for score rather than for genuine compliance. Implementations MUST ensure that governance scoring is opaque to the evaluated agent.

---

## 13. Relationship to Other Standards

### 13.1 PNGE (Protocol-Native Governance Execution)

PNGE defines the governance execution pattern: authority hierarchy, refusal semantics, and protocol-native constraints. DSI provides the quantitative scoring layer within a PNGE-conformant system. DSI does not require PNGE, but the combination provides governance execution with measurable divergence tracking.

### 13.2 SSI (Sovereign Synthetic Intelligence Protocol)

SSI defines cryptographic audit provenance: hash-chained decision records, append-only logs, and tamper detection. DSI evaluation outputs are natural inputs to SSI audit records. DSI does not require SSI, but the combination provides scored governance with cryptographic verifiability.

### 13.3 CVSS (Common Vulnerability Scoring System)

DSI follows a similar architectural philosophy to CVSS: an open scoring methodology with implementation-specific application. CVSS defines how to score vulnerabilities; individual scanners implement detection. DSI defines how to score governance divergence; individual governance engines implement detection and enforcement.

---

## 14. Versioning and Evolution

### 14.1 Specification Versioning

This specification uses semantic versioning:

- **Patch** (1.0.x): Editorial corrections, clarifications
- **Minor** (1.x.0): Backward-compatible additions (new optional fields, conformance guidance)
- **Major** (x.0.0): Breaking changes (new required components, modified formula, changed verdict types)

### 14.2 Stability Guarantees

The following elements are stable in DSI v1.x and MUST NOT change in minor versions:

- The four-component scoring formula structure
- The four verdict types
- The eight action types
- The three risk tiers
- The three chain risk pattern definitions
- The governance capsule required fields

---

## Appendix A: Informative Examples

See [EXAMPLES/](EXAMPLES/) for illustrative DSI evaluations. Examples are deliberately simplified and do not reflect operational configurations. They demonstrate score computation mechanics only.

## Appendix B: JSON Schema

See [JSON_SCHEMA/](JSON_SCHEMA/) for machine-readable schema definitions of DSI evaluation outputs, governance capsules, and audit record extensions.

## Appendix C: References

- [PNGE Standard](https://github.com/dgp-standard/pnge) — Protocol-Native Governance Execution
- [SSI Protocol](https://github.com/dgp-standard/ssi) — Sovereign Synthetic Intelligence Protocol
- [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt) — Key Words for Use in RFCs
- [Semantic Versioning 2.0.0](https://semver.org/) — Version numbering standard
- [CVSS v4.0](https://www.first.org/cvss/v4.0/specification-document) — Architectural precedent for open scoring standards

---

**Copyright 2026 DeAlgo Foundation. Licensed under Apache License 2.0.**
