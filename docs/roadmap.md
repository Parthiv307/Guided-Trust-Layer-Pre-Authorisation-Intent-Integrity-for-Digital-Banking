# 12-Week Prototype Roadmap & Decision Log

Weeks are relative. Compress to the real deadline if needed, but **keep the order and the Go/No-Go gates**. Do not start the next phase until the preceding gate passes.

---

## Week-by-Week Execution Plan

| Week | Focus | Deliverables | Exit Check / Gate |
| :--- | :--- | :--- | :--- |
| **1** | **Foundations** | Protocol spec; Envelope v1 schema; threat model; repo, CI, environments; seed-data plan; metric definitions; pick persona, market, and 2–3 languages | Spec reviewed; threat model covers every boundary |
| **2** | **Envelope Engine** | Canonical JSON, hashing, Ed25519 signing, nonce, expiry; UPI payload parser; DB v1 with migrations; auth skeleton | Tamper test fails correctly; parser handles malformed payloads |
| **3** | **Registry & Decisions** | Merchant, account, and QR-fingerprint tables; admin seeding; deterministic comparator; decision engine v1; sandboxed LLM intent parser with schema validation | Seeded cases return expected outcomes; malformed LLM output is discarded |
| **4** | **Integrity Spine (Part 1)** | Change control; replay protection; idempotency; sandbox PSP adapter; authenticated provider-event ingestion | **Gate A:** Envelope → handoff → event works end-to-end in sandbox |
| **5** | **Merchant Echo** | Merchant PWA; signed challenge & response; timeout semantics; rate limits; optional speaker announcement | Echo confirms, denies, and times out correctly; latency measured |
| **6** | **Integrity Spine (Part 2)** | Reconciliation engine and statuses; evidence ledger with hash chain; VISR issue and verify endpoint | **Gate B:** Forged or altered receipts and events are detected |
| **7** | **Android App** | Intent capture (voice & touch); Consequence Card; decision & status screens; STT & TTS in chosen languages | A new user completes a payment review unaided |
| **8** | **Supporting Features** | Scam Brake context signals & cooling-off; Ask Someone with UPI Circle hand-off; admin console & exception queue | **Gate C:** Full demo journey works in the app |
| **9** | **Security Hardening** | Play Integrity, pinning, row-level security, key management, ASVS pass, AI prompt-injection suite | No critical findings open |
| **10** | **Reliability & Red Team** | Delayed, duplicate, and out-of-order events; run full 24-scenario red-team matrix; language accuracy evaluation | **Gate D:** Matrix passes at agreed thresholds |
| **11** | **Pilot Readiness** | Seed real merchants in pilot market; user-test sessions; partner API & SDK spec; DPDP notice & consent flow; Evidence Pack | Pilot checklist complete |
| **12** | **Evaluate & Present** | Final metrics; demo; technical report; pitch; claim check | **Gate E:** Every claim traced to a test or source |

---

## Cut Order (If Behind Schedule)

1. Fake-QR Heatmap and crowd attestations *(already deferred)*.
2. Voice biometrics and any call-audio features *(already deferred)*.
3. Polish on `Ask Someone` and the Evidence Pack.
4. Depth of Scam Brake signals (keep `call-active` and `first-time-payee` only).
5. Number of languages (drop to two).
6. **Never Cut:** Intent Envelope, Place-Anchored QR Registry, Merchant Echo (basic), Transaction Change Control, Settlement Reconciliation, Evidence Ledger, and the Security & Red-Team weeks (Weeks 9–10).

---

## Decision Log: What Changed and Why

| Earlier Plan | Now | Reason |
| :--- | :--- | :--- |
| Six equal modules | Three primitives + an integrity spine | Focus, clearer partner story, lower build cost. |
| Voice biometrics as a pillar | Optional step-up, deferred | Does not touch payment authorisation; voice cloning risk; sensitive data. |
| 50 m GPS geofence as core check | Weak signal only | Spoofable, poor indoors, many vendors within 50 m in a market. |
| Call-audio analysis | Context signals at payment time | Android restricts call audio; Google and Airtel already compete there. |
| Own Trusted Circle | `Ask Someone` + UPI Circle hand-off | UPI Circle already provides delegation. |
| Comprehension quiz on every payment | Consequence Card on anomaly or for new users | A constant quiz is patronising and adds friction. |
| React user web app | Android first; web only for merchant PWA & admin | Scope; Echo needs a merchant surface, not a second consumer app. |
| *"All Indian languages"* | Two to three languages with published accuracy | Coverage and accuracy differ by model and language. |
| Hash or receipt as proof | Evidence with provenance and trust grades | A hash protects against later edits, not against false inputs. |
