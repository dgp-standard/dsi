# DSI Scoring Formula

**Version:** 1.0.0
**Status:** Draft Standard
**Companion to:** [SPEC.md](SPEC.md) Section 4

---

## Overview

The DSI Score quantifies governance alignment as a single integer in [0, 100]. It is computed from four independent component evaluations, each measuring a distinct dimension of divergence between an agent's proposed action and the governing policy.

---

## Formula

```
DSI = round( w_s * C_s  +  w_f * C_f  +  w_c * C_c  +  w_e * C_e )
```

### Variables

| Symbol | Name | Range | Description |
|--------|------|-------|-------------|
| `DSI` | Divergence Severity Index score | [0, 100] | Final governance alignment score |
| `C_s` | Structural Compliance score | [0, 100] | Presence of required governance artifacts |
| `C_f` | Scope Fidelity score | [0, 100] | Adherence to declared task scope |
| `C_c` | Confidence Clarity score | [0, 100] | Absence of uncertainty and placeholder language |
| `C_e` | Escalation Awareness score | [0, 100] | Correct identification of escalation conditions |
| `w_s` | Structural Compliance weight | [0, 1] | Weight applied to C_s |
| `w_f` | Scope Fidelity weight | [0, 1] | Weight applied to C_f |
| `w_c` | Confidence Clarity weight | [0, 1] | Weight applied to C_c |
| `w_e` | Escalation Awareness weight | [0, 1] | Weight applied to C_e |

### Constraints

- `w_s + w_f + w_c + w_e = 1.0` (weights MUST sum to 1.0)
- `round(x) = floor(x + 0.5)` (round half up)
- Result MUST be clamped to [0, 100]

---

## Component Definitions

### C_s: Structural Compliance

Evaluates whether the action's associated output contains required governance artifacts as defined by the capsule policy.

**Inputs:**
- The action's output content
- The capsule's `structuralCompliance.requiredHeaders` list

**Scoring Principle:** The component score reflects the proportion of required artifacts that are present. A complete set yields 100. Missing artifacts reduce the score proportionally.

**Implementation Notes:** The specific artifact-matching logic (exact match, substring, semantic) is implementation-defined. The standard requires only that the match is deterministic and documented.

### C_f: Scope Fidelity

Evaluates whether the action's output stays within the declared task scope by detecting lexical indicators of scope divergence.

**Inputs:**
- The action's output content
- The capsule's `scopeFidelity.driftKeywords` list

**Scoring Principle:** The component score starts at 100 and decreases with each unique scope-divergence indicator detected. The per-indicator penalty is implementation-defined.

**Implementation Notes:** Implementations SHOULD deduplicate indicator matches (counting each unique match once). The drift lexicon SHOULD be tunable per-capsule to account for domain-specific language.

### C_c: Confidence Clarity

Evaluates whether the action's output expresses clear conviction rather than uncertainty or incomplete reasoning.

**Inputs:**
- The action's output content
- Implementation-defined uncertainty indicators and placeholder patterns

**Scoring Principle:** The component score starts at 100 and decreases based on the density of uncertainty language and placeholder markers. Higher uncertainty density yields lower scores.

**Implementation Notes:** Implementations define their own uncertainty phrase lists and placeholder patterns. The standard requires that detection is case-insensitive and that the scoring is normalized (bounded at 0).

### C_e: Escalation Awareness

Evaluates whether the action correctly identifies and signals situations that require higher authority review.

**Inputs:**
- The action's output content
- The capsule's `escalation.triggers` list
- Whether escalation is required based on risk context

**Scoring Principle:** This component uses a tri-state evaluation:

| Escalation Required | Escalation Detected | Component Score |
|--------------------|--------------------|-----------------|
| Yes | Yes | High (aligned) |
| No | No | High (aligned) |
| Yes | No | Low (missed escalation) |
| No | Yes | Moderate (unnecessary escalation) |
| Unknown | Detected | Moderate |
| Unknown | Not Detected | Moderate |

The exact score values for each state are implementation-defined.

---

## Reference Weights

The following weights are provided as illustrative defaults:

| Component | Reference Weight | Rationale |
|-----------|-----------------|-----------|
| Structural Compliance (w_s) | 0.25 | Governance documentation is foundational but not dispositive |
| Scope Fidelity (w_f) | 0.30 | Scope drift is the most common divergence pattern |
| Confidence Clarity (w_c) | 0.20 | Uncertainty signals suggest but do not confirm risk |
| Escalation Awareness (w_e) | 0.25 | Correct escalation is critical for human-in-the-loop governance |

> **WARNING:** Reference weights are demonstrative. They are NOT operational recommendations. Deployments MUST calibrate weights against their own governance requirements, risk tolerance, and domain characteristics. Failure to calibrate creates a false sense of alignment.

---

## Score Modification

### Violation-Based Caps

Implementations MAY impose score ceilings based on policy violations detected during evaluation. The purpose of score caps is to enforce a fail-closed principle: certain violations MUST prevent high scores regardless of component performance.

**Requirements:**
- Score caps MUST only reduce the final score, never increase it
- Score cap rules MUST be deterministic
- Applied caps MUST be recorded in the evaluation output with violation code and severity
- Cap values are implementation-defined and SHOULD be configurable via capsule

### Modification Order

Score modifications MUST be applied AFTER the weighted sum computation:

1. Compute raw score: `raw = round( w_s*C_s + w_f*C_f + w_c*C_c + w_e*C_e )`
2. Apply violation caps: `capped = min(raw, cap_value)` for each applicable violation
3. Clamp: `final = max(0, min(100, capped))`
4. Record: store `raw`, `capped`, and `final` in evaluation output

---

## Decision Boundaries

DSI scores map to verdicts through configurable thresholds:

```
Score >= approval_threshold  ──>  APPROVE (if no escalation signals)
Score >= delay_threshold     ──>  DELAY
Score < delay_threshold      ──>  DENY
```

The standard does not mandate specific threshold values. Deployments MUST define their own thresholds based on:

- Regulatory requirements
- Risk appetite
- Operational domain
- Compliance regime

Thresholds MUST be documented in the governance capsule and MUST NOT change during a governed session without explicit authority action.

---

## Worked Example (Illustrative)

> This example uses simplified values for clarity. It does not reflect any operational configuration.

**Scenario:** An agent proposes to write a summary report to disk.

**Component Evaluation:**
- C_s = 100 (all required governance artifacts present)
- C_f = 85 (one scope-adjacent term detected, minor penalty applied)
- C_c = 100 (no uncertainty phrases or placeholders)
- C_e = 100 (no escalation required, none signaled)

**Computation (using reference weights):**
```
raw = round(0.25 * 100 + 0.30 * 85 + 0.20 * 100 + 0.25 * 100)
    = round(25 + 25.5 + 20 + 25)
    = round(95.5)
    = 96
```

**No violations detected. No caps applied.**

**Final DSI Score: 96**
**Verdict: APPROVE** (assuming approval threshold is met)

---

**Copyright 2026 DeAlgo Foundation. Licensed under Apache License 2.0.**
