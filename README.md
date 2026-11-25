# Guided Trust Layer

**Pre-Authorisation Intent Layer (PAIL) and Transaction Intent Integrity Protocol (TII): from verified intent to verified settlement.**

> *Comprehension is the Best Fraud Defence.*

> [!IMPORTANT]
> **Claim Boundary:** This project is a **sandbox prototype** — not a bank, PSP, TPAP, or payment product. It never touches the UPI PIN, never moves money, does not replace UPI or any UPI app, and does not claim to eliminate fraud. Live settlement verification requires an authorised provider integration (see [Security & Privacy](docs/security-and-privacy.md#integration-reality-and-the-evidence-boundary)).

---

## Overview

Guided Trust Layer protects the people most exposed to authorised push-payment (APP) fraud—farmers, elderly users, and first-time digital-payment users. It helps them confirm **who they are paying and why** before they approve, and then records **whether the payment that settled matches the payment they approved**.

### The Single Idea

A **signed Intent Envelope** is created from what the user meant to pay, checked before the UPI PIN is entered, and compared field-by-field against authenticated provider events afterwards. Everything in the system either feeds that object or explains its result.

### Two Halves of One Lifecycle

1. **Before the PIN — Pre-Authorisation Intent Layer (PAIL):**
   - The user states what they mean to pay (via voice or touch).
   - The system checks that the scanned QR/payee fits that intent, the shop, and the amount using the **Place-Anchored QR Registry**, **Merchant Echo**, and lightweight context signals.
   - A deterministic rule engine returns `Allow`, `Nudge`, `Hold`, or `Advise Stop` with plain-language reasons.

2. **At and After Handoff — Transaction Intent Integrity Protocol (TII):**
   - Approval is bound to a signed **Intent Envelope**. Any material change (payee, amount, QR payload, currency) voids the approval and requires fresh review.
   - Authenticated provider events are reconciled against the envelope field-by-field.
   - A **Verified Intent-to-Settlement Receipt (VISR)** is issued and appended to a hash-chained **Intent Evidence Ledger**.

---

## The Gap Covered

While India's digital payment ecosystem already ships bank-verified payee names, voice payments (Hello! UPI), delegated payments (UPI Circle), and device/network call-scam detection, five critical gaps remain:

- **QR versus place:** A bank-verified name proves account ownership, not that the QR belongs to the shop in front of the user. Many small vendors legitimately collect on a personal account, so name matching both misses QR swaps and raises false alarms.
- **Intent versus destination:** Nothing compares what the user meant to pay (and for what) against the QR payload in front of them.
- **Approval versus settlement:** After entering the PIN, users have no structured record proving what settled matches what they reviewed—disputes start from screenshots.
- **Payment-moment coaching signals on any phone:** Call-scam protection exists only on specific devices/networks, not as a pre-payment check on budget handsets.
- **Plain-language consequence:** Clear, non-technical consequence statements in the user's language when something is off.

---

## Repository Structure

```text
.
├── README.md                    # Project overview, scope, and claim discipline
├── STATUS.md                    # Current prototype status, open decisions, source verification
├── LICENSE                      # MIT License
├── .gitignore                   # Python, Node/React, Android/Gradle, secrets, and build ignores
└── docs/
    ├── architecture.md          # Modular monolith, tech stack, data model, and core primitives
    ├── transaction-workflow.md  # 9-step PAIL + TII lifecycle, UX principles, and 3-min demo script
    ├── security-and-privacy.md  # Defence in depth, AI safety, DPDP Act, and regulatory boundaries
    ├── api-contracts.md         # v1 REST API surface, Intent Envelope v1, VISR, and Echo protocol
    ├── pilot-and-validation.md  # Metrics, 24-scenario red-team matrix, pilot design, and risks
    └── roadmap.md               # 12-week execution plan, Gates A–E, cut order, and decision log
```

---

## Scope Tiers

| Tier | Components | Rule |
| :--- | :--- | :--- |
| **1. Core (Must Ship)** | Intent Envelope; Place-Anchored QR Registry; Merchant Echo; deterministic decision engine; Transaction Change Control; Settlement Reconciliation with Evidence Ledger and VISR (sandbox) | These define the product. Never cut. |
| **2. Supporting (Ship Lean)** | Consequence Card (voice & pictograms, 2–3 languages); Scam Brake context signals & cooling-off; Ask Someone hand-off; Evidence Pack; admin console; merchant PWA | Cut depth before cutting existence. Each must serve a Tier 1 primitive. |
| **3. Deferred** | Voice-biometric login; call-audio analysis; Fake-QR Heatmap; own trusted-person circle; large-scale crowd attestations; all-language support | Document as roadmap. Do not build in the first 12 weeks. |
| **Consume, Do Not Build** | Bank-verified payee name; DoT FRI scores; MuleHunter outputs; UPI Circle delegation; MyUPI Pay Safe results | Integrate through a partner when available; mock in the sandbox. |

---

## Commercial Positioning

Guided Trust Layer is positioned as **B2B2C payment-integrity infrastructure** delivered as an **SDK and API** for merchant checkout providers, rural fintech/commerce platforms, PSPs/TPAPs, and bank innovation programmes—not as another consumer UPI app.

---

## Claim Discipline

| Say | Do Not Say |
| :--- | :--- |
| *"We add a checkpoint before the PIN and an evidence trail after it."* | *"Eliminates fraud."* |
| *"UPI verifies the account. We verify the intention."* | *"First in India"* without a prior-art check. |
| *"The AI explains; rules decide; the user and bank keep authority."* | *"Works in all Indian languages."* |
| *"Built for people who do not know the shop's name."* | *"Voice authorises payments."* |
| *"Sandbox prototype; production needs an authorised partner."* | *"A trusted person can cancel a UPI payment."* |
| *"Matched on authenticated provider evidence."* | *"Verified settlement"* from a screenshot or UTR. |
