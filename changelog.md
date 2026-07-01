---
description: Platform updates and release history.
---

# Changelog

All notable updates to 7.Exchange are documented here. This page is updated with each release.

---

## 2026-07-01

### Public API v1 is now available

The 7.Exchange swap API is now public. Integrators can build cross-chain and same-chain swap flows directly against our production endpoints.

What you can do with v1:

- **Discover** supported chains, assets, and routing providers (`GET /api/v1/swap/chains`, `/swap/assets`, `/swap/sources`).
- **Quote** the best available routes for a pair and amount (`POST /api/v1/quote`).
- **Lock** a selected route to get an execution payload (`POST /api/v1/quote/lock`).
- **Execute** the locked quote (`POST /api/v1/quote/execute`).
- **Track and poll** transaction status to completion (`POST /api/v1/transaction/track`, `GET /api/v1/transaction/status`).
- **Read** transaction history and aggregate stats (`GET /api/v1/transaction/history`, `/transaction/stats`).

Conventions you can rely on:

- All integrator endpoints are versioned under `/api/v1` and require no API key.
- Every response carries `X-API-Version` and `X-Request-Id`.
- List endpoints return `{ data, pagination }`; single-resource and action endpoints return `{ data }`; errors always return `{ error }` with a stable, machine-readable `error.code`.
- Amounts are human-readable strings, timestamps are ISO 8601.

Start with the [API Reference](developers/api-reference.html). If you run an affiliate link, pass your `referralCode` on lock and execute to attribute swaps.

We follow a clear versioning policy: additive changes (new fields, new optional parameters, new enum values) ship within `v1`; breaking changes will only arrive under a new path version, and every API-affecting changelog entry will name the version it applies to.

### Providers

- **Stargate is available again as a routing provider.** 

### Wallet

- **Keystore now supports importing and exporting mnemonics**, so you can move a wallet in or out of 7.Exchange using its recovery phrase.

---

{% hint style="info" %}
This changelog will be updated as new features, fixes, and improvements are released. For real-time updates, follow [7.Exchange on X](https://x.com/7exchangeDeFi).
{% endhint %}
