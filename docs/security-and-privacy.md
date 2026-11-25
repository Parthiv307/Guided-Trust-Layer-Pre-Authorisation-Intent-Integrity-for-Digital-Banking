# Security, Privacy, and Compliance

**Goal:** No single failure breaches the system. Use defence in depth, explicit trust boundaries, and automated tests that prove each control. Run **STRIDE** on every boundary before building, and keep the threat model alive.

---

## Security Controls Matrix

| Layer | Controls |
| :--- | :--- |
| **Device & Client** | Hardware-backed keys (Android Keystore) and device binding; Play Integrity attestation on sensitive calls; treat rooted, emulated, or overlay-hijacked devices as high risk; certificate pinning; `FLAG_SECURE` on sensitive screens; no sensitive data in logs. Every client check is repeated server-side. |
| **Identity & Session** | Short-lived tokens with rotation; step-up authentication for high-risk actions; per-request nonces and idempotency keys; single-use approvals. |
| **API** | Schema validation, rate limiting, WAF, mutual TLS (mTLS) between internal services; OWASP ASVS and MASVS as checklists. |
| **Provider Events** | Authenticated callbacks (mTLS or signatures); event-id and timestamp checks; reject replays; handle duplicates and out-of-order events idempotently. |
| **Keys** | Signing keys only in KMS or HSM; rotation and key-version tracking; restricted signing access; no keys in code. |
| **Data** | TLS 1.3 in transit; AES-256 at rest; field-level encryption for payee address and phone; keyed hashing (HMAC with secret pepper) for phone identifiers; row-level security (RLS); least-privilege DB roles; secrets in a vault. |
| **Audit & Operations** | Append-only, hash-chained audit logs; four-eyes approval for registry changes; dependency scanning, SAST, DAST, signed builds, SBOM; incident runbook; key-rotation drills; backups tested by restoring; periodic penetration tests. |

---

## AI-Specific Safety

- **All text reaching the LLM is untrusted data:** Payee names, UPI notes, pasted messages, and transcripts can carry injected instructions.
- **No tools, no write access:** The LLM has no tools and no write access. It returns schema-validated JSON only; anything malformed is discarded.
- **Deterministic authority:** The deterministic comparator makes every decision; LLM output cannot widen an allowance or change a payee.
- **Red-teaming:** Prompts for injection, language switching, and look-alike characters are part of the automated test suite.

---

## Privacy and the DPDP Act

The DPDP Rules were notified in November 2025 with an 18-month phase-in. Consent-manager provisions took effect in November 2026, and the main duties—including security safeguards, notices, and breach reporting—apply from **13 May 2027** *(confirm final dates with legal counsel)*. Build to that standard from day one:

- Collect only transaction data needed; obtain verifiable, specific consent; keep a consent record and version (`consent_records`).
- Never store UPI PINs or OTPs. Do not store raw audio by default. Define retention policies and honour deletion requests.
- No continuous call recording. No monetisation of transaction data.
- DPDP has no special biometric class, but treat any voice template as high risk; voice biometrics stay **deferred**.
- Prepare a plain-language privacy notice, a grievance contact, and a breach-response procedure before any pilot.

---

## Regulatory Boundaries

- Never handle, request, or store the UPI PIN. Real payments go through a licensed PSP or TPAP.
- Do not rely on user-submitted merchant data alone as verification.
- A trusted person is never shown as able to cancel a UPI payment; delegation goes through **UPI Circle**.
- Voice identity never substitutes for regulated authentication.

---

## Integration Reality and the Evidence Boundary

A standalone Android app cannot subscribe to UPI settlement events. Production callbacks and payment data sit with authorised PSPs, TPAPs, banks, and aggregators. The prototype must explicitly state what is simulated.

| Level | What It Is | What You May Claim |
| :--- | :--- | :--- |
| **1. Hackathon / Sandbox Prototype** | Simulated PSP and sandbox with test credentials and controlled scenarios | The protocol works end-to-end on sandbox events. Receipts are stamped `SANDBOX`. |
| **2. Pilot Integration** | An authorised checkout provider, aggregator, or fintech supplies supported handoff and authenticated events | Verified outcomes for payments through that partner only. |
| **3. Production Integration** | Formal partnership with a bank, PSP, TPAP, or aggregator; approved APIs; security, privacy, and regulatory review | Verified outcomes within the integrated flows and no wider. |

> [!CAUTION]
> **Critical Claim Boundary:** Without trusted provider-side events, the system cannot guarantee a transaction settled as intended. A user-supplied screenshot or manually entered UTR is **not** independently verified evidence. State this in documentation and on stage.
