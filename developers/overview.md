---
description: Integrate 7.Exchange routing through the live REST API and supporting developer tools.
---

# Developers

The 7.Exchange backend API is live and ready for integration. Wallets, dApps, trading tools, and partners can use the REST API to discover supported infrastructure, request quotes, lock a selected route, build an execution payload, and track transaction status.

{% hint style="info" %}
The canonical machine-readable API spec is served by the backend at `/api/docs.json`, and the interactive Swagger UI is available at `/api/docs`.
{% endhint %}

## Integration surfaces

| Surface | Current status | Use it for |
|---|---|---|
| **REST API** | Live | Quote discovery, route locking, execution payloads, transaction tracking, profiles, referrals, rewards, and notifications. |
| **OpenAPI / Swagger** | Live | Generating clients, inspecting schemas, and checking the backend route surface. |
| **TypeScript helper layer** | Bring your own | Use `fetch` or your preferred HTTP client today. See [SDK](sdk.md) for a small TypeScript wrapper pattern. |
| **Widget** | API-based integration today | Build your own embedded swap UI on top of the REST API. See [Widget](widget.md). |
| **Partner integration** | API-based integration today | Attribute swaps with referral codes and track partner-facing referral data. See [Partner Integration](partner-integration.md). |

## Base URLs

| Environment | Base URL |
|---|---|
| Production | `https://api.7.exchange` |
| Development | `https://api-dev.7.exchange` |
| Local backend | `http://localhost:3001` |
| Local webapp proxy | `http://localhost:3000` |

All endpoint paths in this section are relative to the API base URL. For example, `POST /api/quote` becomes:

```bash
curl -s https://api.7.exchange/api/quote
```

## Core swap flow

1. Discover available chains and tokens with `GET /api/swap/chains` and `GET /api/swap/assets`.
2. Request route quotes with `POST /api/quote`.
3. Choose one returned provider route.
4. Lock that route with `POST /api/quote/lock`.
5. Execute the locked quote with `POST /api/quote/execute`.
6. Track the resulting transaction with `GET /api/transaction/status` or `GET /api/transaction/history`.

The current runtime mode is provider-first. Diamond router infrastructure remains in the backend for future deployment work, but production swaps are executed through provider APIs by default.

## Authentication overview

Many discovery and swap endpoints are public. User profile, referral account, and private notification endpoints require a bearer token returned by wallet authentication endpoints. Operational and admin endpoints require an `x-api-key` header.

| Auth type | Header | Used by |
|---|---|---|
| Public | None | Metadata, quotes, route locking, execution, public transactions, public rewards, and public referral stats. |
| User session | `Authorization: Bearer <token>` | Profile, wallet linking, referral account, private notifications, and logout. |
| API key | `x-api-key: <key>` | Provider recovery metadata, notification publishing, rewards admin, and referral payout admin. |

{% hint style="warning" %}
Never expose API keys in browser code. API-key endpoints are intended for trusted backend or operational use.
{% endhint %}

## Start here

- Read the full [API Reference](api-reference.md).
- Use [SDK](sdk.md) for a TypeScript `fetch` helper pattern.
- Use [Partner Integration](partner-integration.md) if you need referral attribution.
