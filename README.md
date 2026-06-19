> ### 🏛️ The Autonomous Enterprise Platform
> **Governance Layer** → [The Autonomous Enterprise](https://raosiddharthp.github.io/The-Autonomous-Enterprise/) &nbsp;|&nbsp; You are here: **FinRisk Sentinel (M-04)**
>
> **Process Pillars:** [Quote-to-Cash](https://raosiddharthp.github.io/The-Autonomous-Quote-to-Cash/) · [Procure-to-Pay](https://raosiddharthp.github.io/The-Autonomous-Procure-to-Pay/) · [Finance Operations](https://raosiddharthp.github.io/The-Autonomous-Finance-Operations/) · [Supply Chain](https://raosiddharthp.github.io/The-Autonomous-Supply-Chain/)
> **Governance Crown:** [Strategy Dashboard](https://raosiddharthp.github.io/The-Autonomous-Strategy-Dashboard/) · [GreenOps](https://raosiddharthp.github.io/The-Autonomous-GreenOps/) · [Data Governance](https://raosiddharthp.github.io/The-Autonomous-Data-Governance/) · [Compliance Command Centre](https://raosiddharthp.github.io/The-Autonomous-Compliance/)

---

# FinRisk Sentinel

**M-04 · Governance Layer · Cross-Domain Financial Risk**

### Real-time anomaly detection. Dual-reviewer governance. Portfolio-level visibility no single pillar can see.

FinRisk Sentinel is the cross-domain financial risk aggregator of **The Autonomous Enterprise Platform**. It exists at two distinct scopes that share a name because they share underlying data — and the distinction between them is the entire architectural point of this module.

---

## Two Scopes, One Name

**Pillar-level (inside Quote-to-Cash):** operational anomaly detection on individual transactions — payment stream events, contract risk signals from RevRec AI (M-03) and ContractGuard (M-02). Fires per transaction, at point of transaction close. **RevRec AI executes inside Quote-to-Cash, but its compliance governance and audit ownership belong to Finance Operations** — the Finance Controller is the named HITL approver, the same pattern as ContractGuard running in Quote-to-Cash with Legal as the approving authority. Decision horizon: hours.

**Governance-level (this module):** strategic aggregation. FinRisk Sentinel synthesises financial exposure signals from the three financially-exposed pillars — **Quote-to-Cash** (contract risk, CPQ exposure, RevRec anomalies), **Finance Operations** (intercompany and treasury exposure), and **Procure-to-Pay** (procurement commitment exposure) — into a single cross-domain risk posture. Audience: CFO, Board, external auditors. Decision horizon: days to quarters.

The real-world analogy: a bank's transaction fraud detection (fires per card swipe) versus its enterprise risk committee dashboard (portfolio-level credit exposure). Same underlying data. Different aggregation level. Different decision-maker. Different consequence of inaction.

**The pillar fires on individual transactions. This module surfaces portfolio-level risk that no single pillar can see. Both are necessary. Neither replaces the other.**

---

## What It Does

- **Isolation Forest anomaly detection** on BigQuery streaming payment and contract-event data, sourced from Quote-to-Cash, Finance Operations, and Procure-to-Pay simultaneously
- **HITL-08 — dual-reviewer checkpoint.** Every high-confidence anomaly above the action threshold requires two named human approvers, not one, before any downstream write proceeds. This is the only dual-reviewer HITL in the platform — a deliberate response to the asymmetric cost of a false-negative fraud miss versus a false-positive transaction freeze.
- **SHAP feature attribution**, written to BigQuery before the HITL record is created — the same SHAP-before-HITL ordering enforced everywhere else in the platform, so the audit record can never reference an explanation that didn't exist yet.

**A live example from the platform's governance dashboard:** a FinRisk anomaly on the Universitätsklinikum München account — confidence score 0.88 — currently sits in the HITL-08 queue awaiting joint CFO and Finance Controller sign-off, 48 minutes into a 12-minute SLA. This is the same München account that ContractGuard, RevRec AI, and Asset IQ each touch elsewhere in the platform — proof that FinRisk Sentinel's cross-domain visibility is not a hypothetical capability but something that fires on the same real account thread the rest of the platform uses to demonstrate coherence.

---

## Architecture (In Progress)

FinRisk Sentinel's full C4-level architecture documentation — system context, container diagram, state machine, and ADR set — is being brought up to the same depth as the other four governance crown modules. What's structurally settled:

- **Model choice:** Isolation Forest, chosen over deep-learning anomaly detection specifically because it is `TreeExplainer`-compatible — SHAP attribution requires it, and EU AI Act Article 13 requires the SHAP attribution.
- **Dual-reviewer enforcement:** the `sap.post_journal_entry()` write path requires two committed `hitl_id` approval records, not one — a code-level constraint, not a process-level one that a single rushed approver could route around.
- **Decision horizon separation:** the pillar-level and governance-level scopes run on different refresh cadences by design — the pillar reacts in near-real-time, the governance rollup aggregates over a longer window appropriate for board-level reporting.

This README will be updated with full system-context diagrams, the anomaly state machine, and stakeholder rebuttals as that documentation is completed — matching the depth already built out for GreenOps, Data Governance, Strategy Dashboard, and Compliance Command Centre.

---

## Where This Sits in the Platform

FinRisk Sentinel is read by the Strategy Dashboard's Financial Risk panel and by Compliance Command Centre's AI System Risk Register — its EU AI Act Article 9 post-market monitoring log is the one item currently flagged AMBER on the platform's overall compliance posture, six days overdue, surfaced honestly rather than hidden. It depends on Data Governance (M-08) for validated input from all three contributing pillars.

[**← AE Platform Index**](https://raosiddharthp.github.io/The-Autonomous-Enterprise/page-09.html) · [**Strategy Dashboard →**](https://raosiddharthp.github.io/The-Autonomous-Strategy-Dashboard/)

---

*Part of [The Autonomous Enterprise Platform](https://raosiddharthp.github.io/The-Autonomous-Enterprise/) — a system of systems for AI-native enterprise governance, anchored on ClaraVis Medical Systems, a €1.2B German MRI/CT imaging OEM. ClaraVis is a fictional company; all metrics are illustrative of a plausible enterprise at the described scale.*

© 2026 Siddharth Rao Potukuchi
