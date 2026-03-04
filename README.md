# Divergence Severity Index (DSI) v1.0

An open standard for measuring AI governance drift.

---

## What is DSI?

The Divergence Severity Index is a scoring methodology that quantifies how far an autonomous agent's proposed action diverges from its declared governance policy.

DSI produces a normalized score (0-100) across four evaluation dimensions:

- **Structural Compliance** — Are governance artifacts present?
- **Scope Fidelity** — Does the action stay within declared scope?
- **Confidence Clarity** — Is the output decisive, not uncertain?
- **Escalation Awareness** — Are escalation conditions correctly identified?

The score maps to a verdict: **APPROVE**, **DELAY**, **DENY**, or **ESCALATE**.

## What DSI Is Not

DSI is a **scoring language**, not a governance engine.

- It does NOT define how to detect threats
- It does NOT define how to enforce decisions
- It does NOT define how to authenticate operators
- It does NOT prescribe specific threshold values

DSI defines the **measurement**. Implementations define the **mechanism**.

This is by design. CVSS defines how to score vulnerabilities — vulnerability scanners implement detection. DSI defines how to score governance divergence — governance engines implement enforcement.

## Standard Documents

| Document | Description |
|----------|-------------|
| [SPEC.md](SPEC.md) | Formal DSI v1.0 specification |
| [FORMULA.md](FORMULA.md) | Scoring formula, components, and weights |
| [TAXONOMY.md](TAXONOMY.md) | Verdict types, action types, risk tiers, chain risk patterns |
| [ARCHITECTURE.md](ARCHITECTURE.md) | Standard vs. implementation boundary map |
| [JSON_SCHEMA/](JSON_SCHEMA/) | Machine-readable schema definitions |
| [EXAMPLES/](EXAMPLES/) | Illustrative evaluation examples |

## Architecture

DSI is the scoring layer in a composable governance stack:

```
┌──────────────────────────────────────────────────────┐
│  PNGE — "How to govern"                             │
│  Authority hierarchy, refusal semantics, protocol    │
│  supremacy over code                                 │
├──────────────────────────────────────────────────────┤
│  DSI — "How to measure"              ◄── THIS REPO  │
│  Scoring formula, verdicts, action taxonomy,         │
│  risk model, chain risk patterns                     │
├──────────────────────────────────────────────────────┤
│  SSI — "How to prove"                                │
│  Hash-chained records, cryptographic provenance,     │
│  tamper detection                                    │
└──────────────────────────────────────────────────────┘
```

Three independent layers. Each answers a different question. Any can be adopted alone or composed together for the strongest guarantee.

### Open Standard vs. Closed Implementation

```
OPEN (this repo)                 CLOSED (per-vendor)
────────────────                 ───────────────────
Scoring formula                  Scoring engine
Verdict taxonomy                 Detection heuristics
Action classification            Keyword lists & pattern matching
Chain risk pattern names         Thresholds & time windows
Capsule schema                   Enforcement logic
Output format                    Operational calibration
```

DSI defines **what to measure**. Implementations define **how to detect**. This follows the CVSS model: open scoring methodology, proprietary scanners.

## Quick Example

An agent proposes: "Write a summary report to /data/output/report.json"

```
Action Type:    WRITE_FILE
Risk Tier:      MEDIUM

Components:
  Structural Compliance:  100  (all required artifacts present)
  Scope Fidelity:          85  (minor scope-adjacent indicator detected)
  Confidence Clarity:     100  (no uncertainty language)
  Escalation Awareness:   100  (no escalation needed, none signaled)

DSI Score: 96
Verdict:   APPROVE
```

## Conformance Levels

| Level | What it requires |
|-------|-----------------|
| **DSI Basic** | Scoring formula + verdict taxonomy + action classification |
| **DSI Standard** | Basic + risk model + governance capsules + determinism |
| **DSI Full** | Standard + chain risk evaluation + audit integration |

## Related Standards

- **[PNGE](https://github.com/dgp-standard/pnge)** — Protocol-Native Governance Execution
- **[SSI Protocol](https://github.com/dgp-standard/ssi)** — Sovereign Synthetic Intelligence Protocol

## License

Apache License 2.0. See [LICENSE.md](LICENSE.md).

---

**Copyright 2026 DeAlgo Foundation.**
