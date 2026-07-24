---
name: Create a Bitpowr wallet and generate a deposit address
description: Provision a vault account (wallet), attach an asset, and generate a receive address to start accepting crypto deposits.
api: openapi/bitpowr-technologies-inc-openapi.yml
operations:
  - Createaccounts
  - Addassets
  - Createaddresses
  - Getaddressesbalance
---

# Create a wallet and generate a deposit address

Use this flow to stand up a Bitpowr wallet and get a blockchain address that can receive funds.

## Auth & environment
- Send `Authorization: Bearer <token>` where the token is `base64(public_key:secret_key)`, or use a dashboard API key as `Authorization: Basic <API_KEY>`.
- All requests are HTTPS-only. Keys are environment-bound: a `test_`-prefixed key operates on testnet; a live key operates on mainnet.
- Base URL: `https://developers.bitpowr.com/api/v1`.

## Steps
1. **Create the account (wallet)** — `POST /accounts` (`Createaccounts`). Capture the returned account `uid`.
2. **Attach an asset** — `POST /assets` (`Addassets`), associating the coin/token (e.g. a supported chain asset) with the account.
3. **Generate a receive address** — `POST /addresses` (`Createaddresses`) for the account/asset. Capture the address string.
4. **(Optional) Confirm it's live** — `GET /addresses/.../balance` (`Getaddressesbalance`) to read the address balance.

## Conventions & error handling
- List responses are paginated with `page` / `perPage` / `orderBy` and return `page` / `totalPage` headers.
- On HTTP 429, back off and honor the `Retry-After` header.
- Handle 401 (bad/missing key), 400 (malformed request), 403 (restricted) per `errors/bitpowr-technologies-inc-error-codes.yml`.
- Subscribe to the `address.created` and `accounts.created` webhooks (see `asyncapi/bitpowr-technologies-inc-webhooks.yml`) rather than polling.
