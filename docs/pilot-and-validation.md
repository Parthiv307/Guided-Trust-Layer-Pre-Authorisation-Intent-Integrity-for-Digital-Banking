# Pilot, Validation, Commercial Path & Risks

## Testing and Metrics

The system is judged by measured results with defined denominators, conditions, and evidence sources. Targets below are proposed starting values to be calibrated in Weeks 3 and 10.

| Metric | What It Measures | Proposed Starting Target |
| :--- | :--- | :--- |
| **Seeded-attack detection** | Payee swap, amount change, QR payload change, replay in test suite | **100%** of seeded cases (deterministic) |
| **False invalidation rate** | Legitimate payments wrongly voided | **< 1%** |
| **False-warning rate** | Legitimate payments shown a `Nudge` or `Hold` | **< 5%**, tuned downward |
| **Added latency** | Median extra time per payment (no Echo) | **< 3 s** |
| **Echo latency** | Time to confirmed, denied, or timeout | **p95 < 5 s** (stretch: 3 s) |
| **Echo confirmation rate** | Share of challenges answered in time | Measure and report |
| **Replay rejection** | Reused or expired envelopes and events | **100%** |
| **Receipt tamper detection** | Modified receipts or ledger entries | **100%** |
| **Settlement matching rate** | Eligible authenticated events reconciled | Report with denominator |
| **Comprehension** | Users who correctly say where the money goes after the card | **>= 85%** |
| **Task completion** | Users who complete a payment review unaided | **>= 90%** |
| **Language accuracy** | Word error rate per language and accent group | Publish; no cross-language claims |

---

## 24-Scenario Red-Team Matrix

| # | Scenario | Expected Behaviour |
| :--- | :--- | :--- |
| 1 | Genuine shop QR, registered account | Verified match; no friction |
| 2 | Sticker pasted over genuine QR | Registry mismatch + failed Echo; `Hold` |
| 3 | Owner collects on declared personal address | Verified via declared accounts; no false alarm |
| 4 | Merchant changes account legitimately | Cooling period and merchant confirmation; `Nudge`, not block |
| 5 | Unregistered small shop | `"Unregistered"`; neutral wording |
| 6 | Look-alike payee address with swapped characters | Similarity flag; `Nudge` |
| 7 | Change the payee after approval | Approval voided; re-review required |
| 8 | Increase or decrease the approved amount | Approval voided; changed amount explained |
| 9 | Change QR payload between review and handoff | Mismatch detected; re-review |
| 10 | Replay an expired or already-used envelope or approval | Rejected |
| 11 | Forge a provider callback | Authentication fails; event rejected |
| 12 | Same provider event sent twice; events out of order | Idempotent handling; correct final state |
| 13 | Pending, failed, reversed, and refunded events | Each shown distinctly; never as success |
| 14 | Network drops during payment; user retries | Existing intent checked; `UNVERIFIED` shown; duplicate prevented |
| 15 | Merchant Echo response forged or replayed | Signature and nonce checks fail |
| 16 | Fake "bank officer" coaching during a call | Active call, new payee, and urgency raise risk; cooling-off |
| 17 | Screen share or remote-access app active | `Hold` with explicit warning |
| 18 | Prompt injection in payee note or message text | Ignored; schema validation rejects |
| 19 | Spoofed GPS | Treated as weak; other signals decide |
| 20 | Access another customer's receipt | Denied; logged |
| 21 | Tamper with client-side transaction data | Server revalidation detects it |
| 22 | Crowd attestation by fake accounts (if enabled) | Weighting and caps limit influence |
| 23 | Same merchant reference, similar displayed name | Reference compared, not name; mismatch flagged |
| 24 | Low-literacy user confused by a warning | Pictogram + audio; `Ask Someone` path |

---

## Pilot Design

- **Scope:** One persona (a farmer buying inputs), one market, 10–20 merchants, 30–50 users, 2–4 weeks.
- **Money:** Sandbox or test money only until a licensed partner is signed. Attack scenarios are staged with consent.
- **Pre-seed:** Load merchants, accounts, and QR fingerprints before day one so Echo has something to confirm against.
- **Baseline:** Compare detection and comprehension against a name-only check.
- **Measure:** Every metric above, plus Echo response time, merchant opt-out rate, and user trust feedback.
- **Ethics & Consent:** Explicit consent, plain-language notice, no raw audio retained, and a clear way to withdraw.
- **Output:** A short results pack with numbers, limits, and a partner proposal—not slides alone.

---

## Commercial Path and Partners

Position Guided Trust Layer as **B2B2C payment-integrity infrastructure** delivered as an SDK and API, not as another consumer UPI app.

| Partner | What They Get | First Ask |
| :--- | :--- | :--- |
| **Merchant checkout provider / aggregator** | Intent binding, change detection, integrity receipts in supported flows | Sandbox access and a pilot flow |
| **Rural fintech or commerce platform** | Accessible interface + Echo and registry for its merchants | A pilot market and merchant list |
| **PSP or TPAP** | SDK components: envelope, change control, receipt | Technical review and event access |
| **Bank innovation programme** | Structured evidence for dispute handling and APP-fraud review | A sandbox and a mentor |
| **Dispute & insurance teams** | A consistent evidence trail | Format feedback |

- **Entry order:** Merchant checkout integration → regional fintech / rural-commerce platform → payment aggregator → bank programme → broader provider integrations.
- **Revenue:** Enterprise licence, SDK licence, usage/volume pricing, premium audit & exception tooling, support. Never monetise customer transaction data.
- **Evidence support, not legal proof:** A receipt supports investigation; it does not by itself establish liability or guarantee a dispute outcome.

---

## Risks and Mitigations

| Risk | Mitigation |
| :--- | :--- |
| **Adoption:** Echo and registry need merchants | Pilot one market; admin-seeded registry; make Echo optional and fast. |
| **Partner dependence** for real events and payee resolution | Design adapter interface now; sandbox first; one deep partner. |
| **False positives** teach users to ignore warnings | False-warning budget; `Nudge` by default; publish the rate. |
| **Fake attestations** or false QR reports | Defer at scale; weighting, caps, moderation, and appeal when introduced. |
| **Android permission limits** on call state & accessibility | Request minimum permissions; test against Play policies; degrade gracefully. |
| **Overclaiming novelty or security** | Claim-check in Week 12; prior-art search; every claim traced to a test or source. |
| **Liability** if advice is wrong | Advisory language; user and payment provider keep authority; no accusations. |
| **Regulatory change** (RBI proposals, DPDP phase-in) | Track both; keep the layer complementary and consent-first. |
| **Voice cloning and spoofing** | No voice authentication in scope; if added later, treat as one weak factor. |
| **Scope creep** | Tier rules and strict cut order (see [roadmap.md](roadmap.md)). |
