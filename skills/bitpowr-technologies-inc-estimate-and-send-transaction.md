---
name: Estimate and send a Bitpowr transaction
description: Estimate network fees, create an outbound crypto transaction, and track it to confirmation via status and webhooks.
api: openapi/bitpowr-technologies-inc-openapi.yml
operations:
  - Createestimate
  - Createtransactions
  - Gettransaction
  - Listtransactions
  - Createwhitelistaddress
---

# Estimate and send a transaction

Use this flow to move crypto out of a Bitpowr account safely.

## Auth & environment
- `Authorization: Bearer <base64(public_key:secret_key)>` or `Authorization: Basic <API_KEY>`, HTTPS only.
- Use a `test_` key on testnet to dry-run before running live.
- Base URL: `https://developers.bitpowr.com/api/v1`.

## Steps
1. **(Optional) Whitelist the destination** — `POST /addresses/whitelist` (`Createwhitelistaddress`) if account policy requires whitelisted payout addresses.
2. **Estimate fees** — `POST` estimate (`Createestimate`) to get the projected network fee for the asset/amount before committing.
3. **Create the transaction** — `POST /transactions` (`Createtransactions`) with the source account/asset, destination address, and amount.
4. **Track status** — `GET /transactions/{id}` (`Gettransaction`) or filter with `Listtransactions` (by `accountId`, `assetId`, `address`, `hash`, `asset`).

## Conventions & error handling
- Transactions are asynchronous: rely on the `transaction.*` webhook topic (`transaction.pending_approval`, `transaction.awaiting_confirmation`, `transaction.success`, `transaction.failed`) — see `asyncapi/bitpowr-technologies-inc-webhooks.yml` — instead of tight polling.
- Deposits screened by AML emit `transaction.deposit.aml_flagged`; handle flagged funds per your compliance policy.
- Honor `Retry-After` on 429; branch on 400/401/403 per `errors/bitpowr-technologies-inc-error-codes.yml`.
- No idempotency-key is documented — de-duplicate on your side before retrying a create.
