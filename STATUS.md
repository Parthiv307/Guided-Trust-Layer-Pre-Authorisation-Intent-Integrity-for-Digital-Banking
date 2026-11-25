# Project Status

**Document Baseline:** Master Prototype Roadmap v1.0 (25 November 2026)  
**Current Integration Level:** Level 1 — Sandbox Prototype (Planning & Specification Phase)

---

## Current State

This repository contains the structured architecture, protocol specification, security/compliance posture, API contracts, validation matrix, and 12-week build roadmap for **Guided Trust Layer (PAIL + TII)**.

- **Completed:**
  - Product definition, scope tiers, and decision log
  - Core technical design (Intent Envelope v1, Decision Engine, Place-Anchored QR Registry, Merchant Echo, Transaction Change Control, Settlement Reconciliation, VISR, Intent Evidence Ledger)
  - Modular monolith architecture and v1 API surface specification
  - Security, AI safety, DPDP Act (2025 Rules), and regulatory boundary definitions
  - 24-scenario red-team matrix, target metrics, and pilot design
- **In Progress / Upcoming (Week 1–12):**
  - Implementation of the modular monolith backend (Python / FastAPI), Android app (Kotlin / Jetpack Compose), and Merchant/Admin PWA (React / TypeScript) across Gates A–E (see [roadmap.md](docs/roadmap.md)).

---

## Decisions Needed This Week

Before starting implementation in Week 1, the following decisions must be locked:

1. **Pilot persona, market, and languages:** Select the specific pilot market and the 2–3 supported languages.
2. **Sandbox PSP / partner adapter:** Decide which sandbox PSP or partner adapter to build against first.
3. **Ownership:** Assign owners for each Tier 1 primitive and for security/threat-modelling work.
4. **Prior-art & patent search:** Assign an owner and target date before making any novelty or "first" claims.

---

## Sources Consulted & Verification Status

The roadmap and documentation rely on the following regulatory and ecosystem sources. Items marked **Not independently verified** must be verified against primary sources before external presentation.

| Topic | Source Type | Status |
| :--- | :--- | :--- |
| RBI discussion paper (9 April 2026): four options, ₹10,000 one-hour lag, 2025 NCRP figures (~28 lakh complaints worth ₹22,931 crore for digital payments generally) | RBI publication page and press coverage | Checked directly |
| UPI Circle, full delegation (₹15,000/month), IoT extension | NPCI and BHIM announcements, press | Checked |
| BHIM MyUPI and Pay Safe; Hello! UPI; bank-verified names | NPCI, BHIM, press and partner pages | Checked |
| DoT Financial Fraud Risk Indicator; RBI advisory to banks | DoT and PIB material, press | Checked |
| Google on-device scam detection; Airtel network detection | Press and GSMA case study | Checked |
| DPDP Rules 2025 phase-in dates (notified Nov 2025; consent-manager provisions Nov 2026; main duties 13 May 2027) | PIB and law-firm summaries | Checked; confirm final dates with counsel |
| NPCI Unified Agent Protocol | Business Standard report | Reported, not final |
| DPIP: seven banks onboarded to Phase I pilot as of 31 March 2026 | Earlier project research | **Not independently verified** |
| NPCI October 2026 UPI statistics | Earlier project research | **Not independently verified** (omitted from claims) |
| RBI 2025 principle-based authentication framework | Earlier project research | **Not independently verified**; confirm before citing |
| Payment-provider callback restrictions for third parties | Earlier project research (Google Pay UPI callback docs) | **Not independently verified**; confirm with a partner |
