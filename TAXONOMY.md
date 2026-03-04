# DSI Classification Taxonomy

**Version:** 1.0.0
**Status:** Draft Standard
**Companion to:** [SPEC.md](SPEC.md) Sections 5, 6, 7, 8

---

## Overview

This document defines the classification systems used by DSI: verdict types, action types, risk tiers, and chain risk patterns. These taxonomies are stable in DSI v1.x and provide the shared vocabulary for governance scoring across implementations.

---

## 1. Verdict Types

DSI defines four mutually exclusive verdict types. Every evaluation MUST produce exactly one.

### APPROVE

- **Meaning:** The proposed action is aligned with governance policy and may proceed to execution.
- **Conditions:** DSI score meets or exceeds the approval threshold; no escalation signals are active; risk tier does not require mandatory escalation.
- **Post-verdict action:** The action MAY be executed within its declared scope.

### DELAY

- **Meaning:** The proposed action shows partial divergence from governance policy. It is held for a cooling period or manual review.
- **Conditions:** DSI score falls between the delay and approval thresholds; no critical violations are present.
- **Post-verdict action:** The action MUST NOT proceed automatically. A human reviewer or time-based policy MAY re-evaluate.

### DENY

- **Meaning:** The proposed action violates governance policy and MUST NOT be executed.
- **Conditions:** DSI score falls below the delay threshold, OR critical violations impose a score cap below the delay threshold.
- **Post-verdict action:** The action MUST be blocked. The denial reason MUST be recorded in the audit trail.

### ESCALATE

- **Meaning:** The proposed action requires review by a higher authority (founder, security team, compliance officer) regardless of the DSI score.
- **Conditions:** Escalation signals are detected AND the risk tier or policy requires escalation; OR chain risk patterns exceed the policy-defined threshold.
- **Post-verdict action:** The action MUST be held pending explicit authorization from the designated authority. The escalation request MUST include the full evaluation output.

### Verdict Precedence

When multiple conditions are satisfied simultaneously, verdict determination follows this strict precedence:

```
ESCALATE  >  DENY  >  DELAY  >  APPROVE
```

The most restrictive applicable verdict always wins.

---

## 2. Action Types

DSI defines eight canonical action types for classifying agent intent. These are the atomic units of governance evaluation.

### READ_FILE

Reading, querying, or inspecting data from files, databases, or data stores. Read operations do not modify system state.

- **Default Risk:** LOW
- **Reversibility:** Inherently reversible (no state change)
- **Financial Impact:** None

### WRITE_FILE

Creating, modifying, or deleting file content. Includes database writes, configuration updates, and log modifications.

- **Default Risk:** MEDIUM
- **Reversibility:** Potentially irreversible (data overwrite, deletion)
- **Financial Impact:** None (direct)

### RUN_COMMAND

Executing system commands, shell processes, scripts, or CLI tools. Includes process management and service control.

- **Default Risk:** HIGH
- **Reversibility:** Potentially irreversible (system state changes, process effects)
- **Financial Impact:** None (direct)

### WEB_REQUEST

Making outbound HTTP/HTTPS requests to external services. Includes API calls, webhooks, and data submissions.

- **Default Risk:** LOW
- **Reversibility:** Reversible (request can be idempotent) but data may leave the system boundary
- **Financial Impact:** None (direct), but outbound data transfer may have compliance implications

### BROWSER_CONTROL

Programmatic interaction with a web browser. Includes page navigation, form submission, screenshot capture, and DOM manipulation.

- **Default Risk:** MEDIUM
- **Reversibility:** Generally reversible for read-only browser operations; form submissions may have side effects
- **Financial Impact:** None (unless combined with payment actions)

### PAYMENT

Initiating, authorizing, or executing monetary transactions. Includes invoice creation, payment processing, and fund transfers.

- **Default Risk:** HIGH
- **Reversibility:** Irreversible in most financial systems
- **Financial Impact:** Direct monetary impact

### TRANSFER_ASSET

Moving non-monetary assets between systems, accounts, or storage locations. Includes cloud uploads, cross-account migrations, data exports, and snapshot transfers.

- **Default Risk:** HIGH
- **Reversibility:** Potentially irreversible (data in external systems cannot be recalled)
- **Financial Impact:** Indirect (asset value, data sovereignty)

### OTHER

Any action that does not fit the above seven categories. Used as a fallback for novel or unclassified operations.

- **Default Risk:** LOW
- **Reversibility:** Unknown
- **Financial Impact:** Unknown

### Multi-Action Intents

A single agent intent MAY map to multiple action types. For example, "read the database and upload results to S3" maps to both READ_FILE and TRANSFER_ASSET. When evaluating multi-action intents:

- The risk tier MUST be the highest tier among all inferred action types
- Irreversibility is TRUE if any inferred action is irreversible
- Financial impact is TRUE if any inferred action has financial impact

---

## 3. Risk Tiers

### LOW

The action is read-only, reversible, or isolated. It does not modify persistent state, transfer data outside the system boundary, or affect financial systems.

**Governance posture:** Minimal oversight. Automated approval is appropriate for policy-aligned actions.

### MEDIUM

The action modifies state within a bounded, recoverable scope. It may write data, control browsers, or make changes that can be reversed with effort.

**Governance posture:** Standard oversight. Automated approval with drift monitoring. Anomalies trigger DELAY.

### HIGH

The action is irreversible, involves financial transactions, affects security boundaries, or transfers data outside the system's control. Once executed, the action cannot be undone.

**Governance posture:** Elevated oversight. Mandatory escalation review for actions requiring high authority. Chain risk monitoring is critical.

### Risk Tier Properties

| Property | LOW | MEDIUM | HIGH |
|----------|-----|--------|------|
| Reversible | Yes | Partially | No |
| Financial impact | No | No | Possible |
| Escalation eligible | Rarely | Sometimes | Often |
| Chain risk relevant | Monitored | Monitored | Critical |

---

## 4. Chain Risk Patterns

Chain risk patterns detect behavioral anomalies that emerge across sequences of governance decisions. Individual actions may pass evaluation, but the pattern of actions over time reveals governance drift.

### APPROVAL_ACCUMULATION

**Definition:** Repeated approvals of actions with moderately elevated scores within a time window.

**What it detects:** An agent executing a sequence of individually-acceptable actions that collectively represent progressive boundary expansion. Each action appears compliant, but the pace and density of approvals suggest the agent is systematically operating at elevated risk without pause.

**Why it matters:** Governance systems that evaluate actions in isolation miss accumulation patterns. An agent that executes 10 medium-risk actions in 5 minutes has a different risk profile than one that executes the same 10 actions over 10 days.

**Implementation guidance:** Define a time window, a score floor (below which actions are not counted), and a count threshold. Pattern severity SHOULD increase with count.

### THRESHOLD_CLUSTERING

**Definition:** A concentration of action scores near the approval/delay decision boundary within a time window.

**What it detects:** An agent whose actions consistently score just above the approval threshold. This pattern suggests the agent has learned (or is testing) the decision boundary and is optimizing for minimum-viable compliance rather than genuine policy alignment.

**Why it matters:** Threshold clustering is an early indicator of adversarial scoring. It is the governance equivalent of a student consistently scoring exactly the minimum passing grade.

**Implementation guidance:** Define a score range around the approval threshold and a count threshold within a time window.

### AUTHORITY_DILUTION

**Definition:** A declining rate of escalation across a session or time window.

**What it detects:** Early actions in a session triggered appropriate escalations, but later actions with similar risk profiles are being approved without escalation. This suggests that oversight is eroding over time, either through policy misconfiguration or adaptive agent behavior.

**Why it matters:** Authority dilution is the most dangerous chain risk pattern because it is self-reinforcing. Each non-escalated approval becomes precedent for the next, creating a progressive erosion of human oversight.

**Implementation guidance:** Compare escalation rates in the earlier and later portions of the evaluation window. Require a minimum record count before applying this pattern.

### Pattern Interaction

Multiple chain risk patterns MAY be active simultaneously. Implementations MUST aggregate pattern severity into a single chain risk score. The aggregation method is implementation-defined, but MUST be:

- Monotonically increasing (more patterns = higher risk)
- Bounded at [0.0, 1.0]
- Deterministic for identical audit histories

---

## 5. Taxonomy Stability

All classifications defined in this document are stable for DSI v1.x. Changes require a major version increment (DSI v2.0+).

| Element | Count | Stable Through |
|---------|-------|---------------|
| Verdict types | 4 | DSI v1.x |
| Action types | 8 | DSI v1.x |
| Risk tiers | 3 | DSI v1.x |
| Chain risk patterns | 3 | DSI v1.x |

Extensions are permitted under the namespacing rules in [SPEC.md](SPEC.md) Section 11.3.

---

**Copyright 2026 DeAlgo Foundation. Licensed under Apache License 2.0.**
