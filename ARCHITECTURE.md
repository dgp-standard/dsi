# DSI Architecture Separation Map

**Version:** 1.0.0
**Status:** Publication Companion
**Purpose:** Define the boundary between open standard and proprietary implementation

---

## The Governance Stack

DSI exists within a three-layer governance architecture. Each layer is an independent, composable standard. Together they form a complete governance stack for autonomous systems.

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                    GOVERNANCE STACK                                  │
│                                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │                                                               │  │
│  │   PNGE — Protocol-Native Governance Execution                │  │
│  │                                                               │  │
│  │   "How to govern"                                            │  │
│  │                                                               │  │
│  │   Defines: Authority hierarchy, refusal semantics,           │  │
│  │   protocol supremacy over code, human override               │  │
│  │                                                               │  │
│  └───────────────────────────┬───────────────────────────────────┘  │
│                              │                                      │
│  ┌───────────────────────────▼───────────────────────────────────┐  │
│  │                                                               │  │
│  │   DSI — Divergence Severity Index                            │  │
│  │                                                               │  │
│  │   "How to measure"                                           │  │
│  │                                                               │  │
│  │   Defines: Scoring formula, verdict taxonomy,                │  │
│  │   action classification, risk model, chain risk patterns     │  │
│  │                                                               │  │
│  └───────────────────────────┬───────────────────────────────────┘  │
│                              │                                      │
│  ┌───────────────────────────▼───────────────────────────────────┐  │
│  │                                                               │  │
│  │   SSI — Sovereign Synthetic Intelligence Protocol            │  │
│  │                                                               │  │
│  │   "How to prove"                                             │  │
│  │                                                               │  │
│  │   Defines: Hash-chained records, cryptographic provenance,   │  │
│  │   tamper detection, append-only audit logs                   │  │
│  │                                                               │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Key principle:** Each layer answers a different question. No layer depends on the others to function. Any layer can be adopted independently. The combination provides the strongest guarantee.

---

## Open vs. Closed Boundary

The governance stack separates what is shared from what is proprietary. This separation is permanent and intentional.

```
                    ┌─────────────────────────────┐
                    │                             │
                    │     OPEN STANDARDS          │
                    │                             │
                    │  PNGE ── governance pattern │
                    │  DSI  ── scoring language   │
                    │  SSI  ── audit protocol     │
                    │                             │
                    │  Anyone can implement.      │
                    │  Anyone can adopt.          │
                    │  Anyone can extend.         │
                    │                             │
════════════════════╪═════════════════════════════╪══════════════════
  STANDARD BOUNDARY │  Published specification    │  THE LINE
════════════════════╪═════════════════════════════╪══════════════════
                    │                             │
                    │     CLOSED ENGINE           │
                    │                             │
                    │  Detection heuristics       │
                    │  Scoring internals          │
                    │  Enforcement logic          │
                    │  Training data              │
                    │  Operational thresholds      │
                    │  Override mechanics          │
                    │                             │
                    │  Proprietary.               │
                    │  Per-vendor.                │
                    │  Competitive moat.          │
                    │                             │
                    └─────────────────────────────┘
```

---

## What Each Layer Publishes vs. Protects

### PNGE (Governance Pattern)

| Published | Protected |
|-----------|-----------|
| Authority hierarchy model | Specific enforcement mechanisms |
| Refusal-as-safety-primitive | Internal refusal decision trees |
| Protocol supremacy over code | Implementation constraint wiring |
| External auditability requirements | Audit infrastructure internals |

### DSI (Scoring Standard)

| Published | Protected |
|-----------|-----------|
| Scoring formula: `DSI = Σ(wᵢ · Cᵢ)` | Component scoring engines |
| Four component dimensions | Penalty constants, cap values |
| Reference weights (illustrative) | Operational weights |
| Verdict taxonomy (4 types) | Verdict derivation internals |
| Action classification (8 types) | Action inference heuristics |
| Risk tier model (3 tiers) | Risk elevation keyword lists |
| Chain risk pattern names (3 patterns) | Detection thresholds, time windows |
| Governance capsule schema | Capsule merge implementation |
| Evaluation output schema | Score modification rules |
| Conformance levels | Internal test corpus |

### SSI (Audit Protocol)

| Published | Protected |
|-----------|-----------|
| Hash chain invariants | Signing key management |
| Append-only record guarantees | Storage implementation |
| Tamper detection principles | Verification optimizations |
| Record schema | Infrastructure topology |

---

## Standard vs. Implementation

This diagram shows the relationship between the open standard and any conforming implementation.

```
    ┌──────────────────────────────────────────────────────────────┐
    │                                                              │
    │                    DSI STANDARD                              │
    │                    (This Repository)                         │
    │                                                              │
    │   Defines:                                                   │
    │   ┌─────────┐ ┌──────────┐ ┌──────────┐ ┌───────────────┐  │
    │   │ Formula │ │ Verdicts │ │ Actions  │ │ Chain Risk    │  │
    │   │         │ │          │ │          │ │ Patterns      │  │
    │   │ 4 comp. │ │ APPROVE  │ │ 8 types  │ │ 3 patterns    │  │
    │   │ weights │ │ DELAY    │ │          │ │               │  │
    │   │ sum = 1 │ │ DENY     │ │ 3 risk   │ │ Aggregate     │  │
    │   │         │ │ ESCALATE │ │ tiers    │ │ scoring       │  │
    │   └─────────┘ └──────────┘ └──────────┘ └───────────────┘  │
    │                                                              │
    │   Publishes:                                                 │
    │   ┌──────────────┐ ┌──────────────┐ ┌────────────────────┐  │
    │   │ JSON Schemas │ │ Capsule Spec │ │ Conformance Levels │  │
    │   └──────────────┘ └──────────────┘ └────────────────────┘  │
    │                                                              │
    └──────────────────────────────┬───────────────────────────────┘
                                   │
                         implements │
                                   │
    ┌──────────────────────────────▼───────────────────────────────┐
    │                                                              │
    │              ANY CONFORMING IMPLEMENTATION                   │
    │                                                              │
    │   Must provide:                                              │
    │   ┌──────────────────┐  ┌───────────────────────────────┐   │
    │   │ Classification   │  │ Detection Heuristics          │   │
    │   │ Engine           │  │ (keywords, patterns, rules)   │   │
    │   │                  │  │                               │   │
    │   │ How intent maps  │  │ Vendor-specific.              │   │
    │   │ to action types  │  │ This is the moat.             │   │
    │   └──────────────────┘  └───────────────────────────────┘   │
    │                                                              │
    │   ┌──────────────────┐  ┌───────────────────────────────┐   │
    │   │ Scoring Engine   │  │ Operational Thresholds        │   │
    │   │                  │  │                               │   │
    │   │ How components   │  │ Approval threshold            │   │
    │   │ are computed     │  │ Delay threshold               │   │
    │   │                  │  │ Score caps                    │   │
    │   │ Penalties, caps, │  │ Chain risk windows            │   │
    │   │ rounding rules   │  │ Count triggers                │   │
    │   └──────────────────┘  └───────────────────────────────┘   │
    │                                                              │
    │   ┌──────────────────┐  ┌───────────────────────────────┐   │
    │   │ Enforcement      │  │ Training Data                 │   │
    │   │ Layer            │  │                               │   │
    │   │                  │  │ Ground truth corpus           │   │
    │   │ How verdicts     │  │ Decision boundary examples    │   │
    │   │ become actions   │  │ Contrastive pairs             │   │
    │   └──────────────────┘  └───────────────────────────────┘   │
    │                                                              │
    └──────────────────────────────────────────────────────────────┘
```

**The standard defines the WHAT. Implementations define the HOW.**

Any organization can build a DSI-conformant governance engine. They must provide their own detection heuristics, scoring internals, and enforcement logic. The standard ensures their scores are comparable and their outputs are interoperable.

---

## Architectural Precedents

This separation model follows established patterns in the industry:

```
    OPEN STANDARD                CLOSED IMPLEMENTATION
    ─────────────                ────────────────────
    CVSS (scoring)         →     Qualys, Tenable, Rapid7 (scanners)
    OAuth 2.0 (auth)       →     Google, Microsoft, Okta (providers)
    HTTP (protocol)        →     Nginx, Apache, Caddy (servers)
    SQL (query language)   →     PostgreSQL, Oracle, MySQL (engines)
    OpenTelemetry (spec)   →     Datadog, Splunk, Grafana (platforms)

    DSI (scoring)          →     [conforming implementations]
```

The standard creates the **category**. Implementations compete **within** the category. The standard author has first-mover advantage and reference-implementation authority.

---

## Composability Model

Each standard in the governance stack can be adopted independently or composed:

```
    ADOPTION PATTERNS
    ─────────────────

    DSI only:
    ┌─────┐
    │ DSI │  Score governance divergence, use any audit system
    └─────┘

    DSI + SSI:
    ┌─────┐
    │ DSI │──▶ Scored governance with cryptographic audit trail
    ├─────┤
    │ SSI │
    └─────┘

    PNGE + DSI:
    ┌──────┐
    │ PNGE │──▶ Protocol-governed systems with quantitative scoring
    ├──────┤
    │ DSI  │
    └──────┘

    Full Stack:
    ┌──────┐
    │ PNGE │──▶ Complete governance: pattern + scoring + proof
    ├──────┤
    │ DSI  │
    ├──────┤
    │ SSI  │
    └──────┘
```

No layer requires another. Each adds a dimension of governance assurance.

---

## The Moat Principle

Publishing a standard does not weaken a competitive position. It strengthens it.

```
    WHAT YOU GIVE AWAY              WHAT YOU KEEP
    ──────────────────              ──────────────

    Scoring language                Scoring engine
    Measurement vocabulary          Detection intelligence
    Output format                   Decision heuristics
    Interoperability contract       Operational thresholds
    Category definition             Category leadership
```

The standard creates demand for the capability. The implementation fulfills it.

Competitors who adopt the standard validate the category. Competitors who reject it define themselves as non-standard. Both outcomes benefit the standard author.

---

## For Implementers

If you are building a DSI-conformant system, you need to provide:

1. **A classification engine** — Maps agent intent to action types. Your detection heuristics are your competitive advantage.

2. **A component scorer** — Computes each of the four component scores. Your penalty logic, keyword lists, and matching algorithms are proprietary.

3. **A verdict derivation engine** — Maps scores to verdicts using your operational thresholds. Your threshold calibration is informed by your deployment environment.

4. **A chain risk analyzer** (for DSI Full conformance) — Detects behavioral patterns across decision history. Your time windows, count thresholds, and severity weights are proprietary.

5. **An audit integration** (for DSI Full conformance) — Records evaluation outputs in an append-only audit chain. SSI Protocol provides a reference, but any tamper-evident log suffices.

The standard tells you **what to output**. You decide **how to compute it**.

---

**Copyright 2026 DeAlgo Foundation. Licensed under Apache License 2.0.**
