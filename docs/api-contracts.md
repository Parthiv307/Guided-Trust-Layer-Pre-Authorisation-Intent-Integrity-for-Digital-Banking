# API Contracts & Data Schemas (v1)

## API Design Principles

- **Unique IDs:** UUIDs for entities (`envelope_id`, `receipt_id`, `challenge_id`, `session_ref`).
- **Integer Money:** All amounts in integer paise (`BIGINT`, e.g., `200000` for ₹2,000.00); never floats.
- **Auth & Isolation:** Authentication and authorisation on every sensitive endpoint; customer data restricted to its owner via row-level security.
- **Strict Validation:** All client inputs treated as untrusted and validated against strict schemas.
- **Authenticated Callbacks:** Provider events require mutual TLS or cryptographic signature verification, plus replay protection (event-id and timestamp checks).
- **Idempotency & Audit:** Idempotency keys on all retryable calls; every state transition logged to the hash-chained ledger.

---

## API Surface (Version 1)

| Method & Path | Responsibility |
| :--- | :--- |
| `POST /v1/intents` \| `GET /v1/intents/{id}` | Create and retrieve an intent from voice or touch input |
| `POST /v1/payee/resolve` | Parse the UPI payload and resolve the payee |
| `POST /v1/registry/lookup` | Retrieve Place-Anchored QR Registry evidence for this payee at this place |
| `POST /v1/echo/challenges` \| `POST /v1/echo/challenges/{id}/respond` | Issue and answer a Merchant Echo challenge |
| `POST /v1/decisions` | Run deterministic rules; return outcome (`Allow`, `Nudge`, `Hold`, `Advise Stop`), reasons, and explanation |
| `POST /v1/intents/{id}/review` \| `/approve` \| `/invalidate` | Record user review, explicit single-use consent, or voiding |
| `POST /v1/handoffs` | Hand off to the sandbox PSP or authorised partner |
| `POST /v1/provider-events` | Receive an authenticated provider callback/event |
| `POST /v1/reconciliation` | Reconcile provider event with the signed Intent Envelope |
| `GET /v1/transactions/{id}/integrity` | Retrieve transaction integrity result |
| `GET /v1/receipts/{id}` \| `/verify` | Retrieve and cryptographically verify a VISR |
| `POST /v1/evidence-packs` | Build a structured dispute pack for bank, helpline, and cybercrime portal |
| `GET /v1/exceptions` \| `POST /v1/exceptions/{id}/resolve` | Exception queue with authorised resolution |
| `Admin: /v1/admin/merchants`, `/accounts`, `/qr-records` | Registry management with four-eyes approval |

---

## Intent Envelope Schema (Version 1)

Canonicalised JSON is hashed (`SHA-256`) and signed (`Ed25519` or approved scheme, key held in KMS/HSM). Every field records its source and trust level.

| Field | Example | Source & Trust |
| :--- | :--- | :--- |
| `envelope_id`, `version` | UUID, `"1.0"` | System |
| `created_at`, `expires_at`, `nonce` | Expiry ~5 minutes; random 128-bit nonce | System |
| `session_ref` | Opaque reference (no raw phone number) | System |
| `payee.vpa` | Payee address from QR payload | QR payload (untrusted until checked) |
| `payee.display_name`, `name_source` | `"ABC Fertilisers"`; `QR` \| `bank-resolved` \| `user-stated` | Tagged per source |
| `payee.merchant_ref`, `category_code` | Merchant/acquirer reference; MCC when present | QR payload or registry |
| `amount_paise`, `currency` | `200000`, `"INR"` (integer paise) | QR payload or user |
| `stated_intent` | Category, max amount, payee description, per-field confidence | User (voice/touch); LLM-parsed |
| `place` | Latitude, longitude, accuracy in metres | Device (weak signal) |
| `registry_ref`, `echo_ref` | Registry result ID; Echo outcome | System |
| `decision`, `reasons` | `Nudge`; `["payee not registered at this place"]` | Rules engine |
| `consent_version`, `approval` | Approval time, single-use approval nonce, device key ID | User + device |
| `signature`, `key_id`, `prev_hash` | Ed25519 over canonical JSON; link to ledger chain | System (key in KMS) |

---

## Merchant Echo Protocol Contract

1. **Trigger:** Weak registry confidence, new QR fingerprint, first-time payee at this place, or amount above threshold.
2. **Challenge (`POST /v1/echo/challenges`):** Server creates `challenge_id`, masked payee address, amount, nonce, and expiry (~20 seconds); sends push notification (FCM) and optional shop-speaker announcement.
3. **Response (`POST /v1/echo/challenges/{id}/respond`):** Merchant taps `Yes` or `No`. Response is signed with a device-bound key and verified for signature, nonce, and expiry.
4. **Result:**
   - `CONFIRMED`: raises confidence.
   - `DENIED`: triggers `Hold` or `Advise Stop` and flags the registry.
   - `TIMEOUT`: means *"unconfirmed, proceed with caution"* (`Nudge`), never a hard block.
5. **Controls:** Per-merchant and per-customer rate limits; quiet hours; opt-out; only masked payee data and amount shared; every challenge logged to the ledger.

---

## VISR Reconciliation Output Example

```json
{
  "integrity_status": "MATCHED",
  "envelope_id": "uuid",
  "provider_reference": "provider-reference",
  "checks": {
    "payee": "MATCH",
    "amount": "MATCH",
    "currency": "MATCH",
    "merchant_reference": "MATCH",
    "settlement_status": "SUCCESS"
  },
  "evidence_grade": "A",
  "receipt_id": "uuid"
}
```

> **Note:** In Level 1 (sandbox prototype), `evidence_grade` is `"S"` and receipts are stamped `SANDBOX`.
