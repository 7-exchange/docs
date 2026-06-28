---
description: REST API for programmatic access to 7.Exchange routing, execution, auth, transactions, referrals, and rewards.
---

# API Reference

{% hint style="info" %}
The 7.Exchange REST API is live. The backend also serves an interactive Swagger UI at `/api/docs` and a machine-readable OpenAPI document at `/api/docs.json`.
{% endhint %}

## Base URLs

| Environment | Base URL |
|---|---|
| Production | `https://api.7.exchange` |
| Development | `https://api-dev.7.exchange` |
| Local backend | `http://localhost:3001` |
| Local webapp proxy | `http://localhost:3000` |

All examples below use the production base URL. Replace it with the development or local URL when testing in those environments.

## Conventions

- All request and response bodies are JSON unless stated otherwise.
- Send `Content-Type: application/json` on `POST` and `PUT` requests.
- Chain identifiers use the backend chain key from `GET /api/swap/chains`, for example `ethereum`, `arbitrum`, or `solana`.
- Token addresses should come from `GET /api/swap/assets`. Native assets may also be represented by `native`, the zero address, `0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee`, or Polygon's native alias `0x0000000000000000000000000000000000001010`.
- `amount` is a positive human-readable token amount string, such as `"50"` or `"0.25"`. The backend resolves token decimals before calling providers.
- `slippage` is expressed in basis points. `100` means 1%, and the accepted range is `0` to `10000`.
- Error responses are either a direct `{ "error": "..." }` object from a controller or the shared error format `{ "success": false, "error": "...", "statusCode": 400, "timestamp": "..." }`.

## Authentication

| Auth type | Header | Applies to |
|---|---|---|
| Public | None | Health, metadata, quote, lock, execute, transaction status/history, public rewards, and public referral stats. |
| User session | `Authorization: Bearer <token>` | Profile, wallet linking, referral account, private notifications, and logout. |
| API key | `x-api-key: <key>` | Provider recovery metadata, public notification publishing, rewards admin, and referral payout admin. |

{% hint style="warning" %}
Keep API keys server-side. Do not ship them in a browser, mobile app bundle, or public repository.
{% endhint %}

## Rate Limits

Current backend rate limits are IP-based:

| Scope | Limit |
|---|---|
| General `/api/*` traffic | 100 requests per minute |
| Transaction history and stats | 30 requests per minute |
| Transaction tracking writes | 5 requests per 5 minutes |
| Strict admin endpoints | 10 requests per 15 minutes |

Responses include standard rate-limit headers.

## Core Swap Flow

The current runtime is provider-first. Diamond router code is retained for future deployment, but live swap execution uses provider APIs and provider-generated execution payloads.

### 1. Discover Chains

```bash
curl -s "https://api.7.exchange/api/swap/chains?perPage=100"
```

Response:

```json
{
  "chains": [
    {
      "key": "ethereum",
      "name": "Ethereum",
      "shortname": "ETH",
      "chainId": 1,
      "image": "https://...",
      "type": "EVM",
      "active": true
    }
  ],
  "page": 1,
  "perPage": 100,
  "total": 1
}
```

### 2. Discover Tokens

```bash
curl -s "https://api.7.exchange/api/swap/assets?chain=ethereum&query=usdc"
```

Response:

```json
{
  "assets": [
    {
      "id": 1,
      "name": "USD Coin",
      "symbol": "USDC",
      "address": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
      "decimals": 6,
      "image": "https://...",
      "chain": "ethereum",
      "isNative": false
    }
  ],
  "page": 1,
  "perPage": 100,
  "total": 1
}
```

### 3. Request Quotes

`POST /api/quote` returns available provider routes. This endpoint is a preview endpoint: if `depositor` or `recipient` is omitted, the backend may use internal preview addresses where supported. For execution safety, always send real participant addresses when you lock and execute.

```bash
curl -s https://api.7.exchange/api/quote \
  -H "Content-Type: application/json" \
  -d '{
    "srcChain": "ethereum",
    "srcAddress": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "dstChain": "arbitrum",
    "dstAddress": "0xaf88d065e77c8cC2239327C5EDb3A432268e5831",
    "amount": "50",
    "slippage": 100,
    "depositor": "0xYourSourceWallet",
    "recipient": "0xYourDestinationWallet"
  }'
```

Request body:

| Field | Required | Description |
|---|---:|---|
| `srcChain` | Yes | Source chain key from `GET /api/swap/chains`. |
| `srcAddress` | Yes | Source token address or native alias. |
| `dstChain` | Yes | Destination chain key. |
| `dstAddress` | Yes | Destination token address or native alias. |
| `amount` | Yes | Human-readable positive token amount. |
| `slippage` | No | Slippage tolerance in basis points. Defaults are provider-dependent when omitted. |
| `decimals` | No | Source token decimals override when the token cannot be resolved from the database. |
| `depositor` | No for preview | Source wallet address. Required for lock and execute. |
| `recipient` | No for preview | Destination wallet address. Required for lock and execute. |
| `exclude` | No | Provider keys to remove from consideration. |
| `referrer` | No | Optional referrer address passed into quote construction. |
| `referralCode` | No | Optional referral code used later for attribution. |
| `namespace` | No | Wallet namespace such as `eip155`, `solana`, or another supported namespace. |

Response:

```json
[
  {
    "provider": "ACROSS_PROTOCOL",
    "quoteId": "provider-specific-id",
    "quote": {},
    "fee": {},
    "exec": {}
  }
]
```

Quote response objects are provider-specific. Choose the provider from the returned route object and pass that exact provider key to `POST /api/quote/lock`.

### 4. Lock a Quote

`POST /api/quote/lock` locks the selected provider quote and stores a one-time execution payload. It requires real `depositor`, `recipient`, and `provider` values.

```bash
curl -s https://api.7.exchange/api/quote/lock \
  -H "Content-Type: application/json" \
  -d '{
    "srcChain": "ethereum",
    "srcAddress": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "dstChain": "arbitrum",
    "dstAddress": "0xaf88d065e77c8cC2239327C5EDb3A432268e5831",
    "amount": "50",
    "slippage": 100,
    "provider": "ACROSS_PROTOCOL",
    "depositor": "0xYourSourceWallet",
    "recipient": "0xYourDestinationWallet",
    "referralCode": "optional-code"
  }'
```

The response may include:

| Field | Description |
|---|---|
| `quoteId` | Locked quote identifier. Required for execute. |
| `provider` | Provider selected for the route. |
| `quote` | Provider-normalized quote details. |
| `exec` | Execution metadata. |
| `providerTarget` | Address to call for provider-first execution, when applicable. |
| `providerCalldata` | Calldata for provider-first execution, when applicable. |
| `value` | Native value required for the transaction, when applicable. |
| `depositAddress` / `depositMemo` | Direct-send instructions for providers that require manual deposit. |

### 5. Execute a Locked Quote

`POST /api/quote/execute` consumes the locked payload. Send the `quoteId` returned by the lock call and the same participant addresses used to lock it.

```bash
curl -s https://api.7.exchange/api/quote/execute \
  -H "Content-Type: application/json" \
  -d '{
    "srcChain": "ethereum",
    "srcAddress": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "dstChain": "arbitrum",
    "dstAddress": "0xaf88d065e77c8cC2239327C5EDb3A432268e5831",
    "amount": "50",
    "slippage": 100,
    "quoteId": "locked-quote-id",
    "provider": "ACROSS_PROTOCOL",
    "depositor": "0xYourSourceWallet",
    "recipient": "0xYourDestinationWallet",
    "referralCode": "optional-code"
  }'
```

Successful responses are provider-specific and include a `transaction` object when the backend can record the swap:

```json
{
  "provider": "ACROSS_PROTOCOL",
  "providerResult": {},
  "quote": {},
  "transaction": {
    "transaction": {
      "id": "public-transaction-id",
      "status": "PENDING"
    }
  }
}
```

If the backend receives enough provider metadata, it starts status tracking automatically for pending transactions.

### 6. Track Status

Use the public transaction ID from the execute response:

```bash
curl -s "https://api.7.exchange/api/transaction/status?transactionId=public-transaction-id"
```

Or look up by hash:

```bash
curl -s "https://api.7.exchange/api/transaction/status?hash=0xTransactionHash"
```

Response:

```json
{
  "transaction": {
    "id": "public-transaction-id",
    "status": "PENDING"
  },
  "final": false
}
```

`final` is `true` when status is `SUCCESS`, `FAILED`, or `REFUND`.

## Endpoint Reference

### System

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/health` | Public | Health check with uptime, environment, and version. |
| `GET` | `/api/docs` | Public | Swagger UI for the live OpenAPI spec. |
| `GET` | `/api/docs.json` | Public | OpenAPI JSON document. |

### Metadata

| Method | Path | Auth | Query | Response |
|---|---|---|---|---|
| `GET` | `/api/swap/chains` | Public | `page`, `perPage`, `query`, `type` | `{ chains, page, perPage, total }` |
| `GET` | `/api/swap/assets` | Public | `page`, `perPage`, `query`, `chain` | `{ assets, page, perPage, total }` |
| `GET` | `/api/swap/sources` | Public | `page`, `perPage`, `type` | `{ sources, page, perPage, total }` |
| `GET` | `/api/swap/integrations` | Public | `page`, `perPage`, `query`, `type` | `{ data, total, counts, skip, perPage }` |

`type` for integrations accepts `ALL`, `EXCHANGE`, `BRIDGE`, `SERVICE`, `CHAIN`, or `WALLET`.

### Quotes and Execution

| Method | Path | Auth | Description |
|---|---|---|---|
| `POST` | `/api/quote` | Public | Return available provider quotes for a token pair and amount. |
| `POST` | `/api/quote/lock` | Public | Lock one provider quote for execution. Requires `provider`, `depositor`, and `recipient`. |
| `POST` | `/api/quote/execute` | Public | Execute a locked quote. Requires `quoteId`, `depositor`, and `recipient`. |
| `POST` | `/api/quote/submit-signature` | Public | Submit LayerZero/Stargate signatures for routes that require external signature submission. |

#### Stargate Signature Submission

`POST /api/quote/submit-signature` only supports Stargate routes.

```bash
curl -s https://api.7.exchange/api/quote/submit-signature \
  -H "Content-Type: application/json" \
  -d '{
    "provider": "STARGATE",
    "providerQuoteId": "quote_or_0x_id",
    "signatures": ["0xSignature"]
  }'
```

Accepted body fields:

| Field | Required | Description |
|---|---:|---|
| `provider` | No | Defaults to `STARGATE`. Any other provider returns `400`. |
| `providerQuoteId` | Yes | Stargate or LayerZero quote identifier. `quoteId` is accepted as an alias. |
| `signatures` | Yes | Array of hex signatures. `signature` is accepted as a single-value alias. |

### Transactions

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/transaction/history` | Public | Public transaction history. Can be scoped by wallet addresses. |
| `GET` | `/api/transaction/status` | Public | Lookup a public transaction by `transactionId` or `hash`. |
| `POST` | `/api/transaction/track` | Public | Attach an inbound transaction hash or provider reference details to a recorded transaction and start tracking. |
| `GET` | `/api/transaction/stats` | Public | Public aggregate successful swap stats. |
| `GET` | `/api/transaction/provider-reference` | API key | Private provider recovery metadata for operational use. |

`GET /api/transaction/history` query parameters:

| Query | Description |
|---|---|
| `addresses` | Repeated or comma-separated wallet addresses. `address`, `walletAddresses`, and `walletAddress` are accepted aliases. |
| `page` / `perPage` | Pagination. `perPage` is capped by backend history rules. |
| `search` | Search string. `query` is accepted as an alias. |
| `statuses` | Repeated or comma-separated `SUCCESS`, `PENDING`, `FAILED`, or `REFUND`. `status` is accepted as an alias. |
| `finalizedOnly` | `true`, `false`, `1`, or `0`. |

`POST /api/transaction/track` body:

| Field | Required | Description |
|---|---:|---|
| `transactionId` | Yes | Public or legacy transaction ID returned by execute. |
| `txHash` | No | Inbound source-chain transaction hash. TON BOC-like payloads are resolved when possible. |
| `provider` | No | Provider key. Defaults to the recorded transaction provider. |
| `chainId`, `sourceChainId`, `destinationChainId`, `originChainId` | No | Chain IDs used by provider status trackers. |
| `depositId`, `depositAddress`, `depositMemo` | No | Direct-send deposit metadata. |
| `requestId`, `channelId`, `providerQuoteId` | No | Provider-specific reference fields. |
| `vaultSwapTxHash`, `vaultTxHash`, `swapId` | No | Chainflip-style swap reference aliases. |

### Wallet Authentication

The API supports an EVM SIWE flow and a multichain challenge flow.

| Method | Path | Auth | Description |
|---|---|---|---|
| `POST` | `/api/auth/nonce` | Public | Issue a nonce for an EVM wallet address. |
| `POST` | `/api/auth/verify` | Public | Verify a SIWE message and signature. Returns a bearer token. |
| `POST` | `/api/auth/challenge` | Public | Issue a multichain wallet challenge. |
| `POST` | `/api/auth/verify-challenge` | Public | Verify a challenge proof. Returns a bearer token. |
| `GET` | `/api/auth/me` | Bearer | Return the current auth context. |
| `POST` | `/api/auth/logout` | Bearer | Revoke the current session. |

EVM nonce request:

```json
{
  "walletAddress": "0xYourWallet"
}
```

SIWE verify request:

```json
{
  "message": {},
  "signature": "0xSignature"
}
```

Multichain challenge request:

```json
{
  "walletAddress": "wallet-address",
  "namespace": "eip155"
}
```

Challenge verify request:

```json
{
  "challengeId": "challenge-id",
  "walletAddress": "wallet-address",
  "namespace": "eip155",
  "proof": {
    "signature": "signature-or-chain-specific-proof"
  }
}
```

Authentication responses include:

```json
{
  "token": "jwt",
  "expiresAt": "2026-06-28T00:00:00.000Z",
  "userId": "user-id",
  "walletAddress": "wallet-address",
  "walletNamespace": "eip155"
}
```

### Profiles

| Method | Path | Auth | Description |
|---|---|---|---|
| `POST` | `/api/profile` | Bearer | Create or update the authenticated user's profile. |
| `GET` | `/api/profile/me` | Bearer | Get the authenticated user's profile. |
| `GET` | `/api/profile/transactions` | Bearer | Get transaction history for the authenticated user. |
| `POST` | `/api/profile/wallets/challenge` | Bearer | Issue a wallet-link challenge for another wallet. |
| `POST` | `/api/profile/wallets/add` | Bearer | Add a wallet after verifying its challenge proof. |
| `POST` | `/api/profile/wallets/remove` | Bearer | Remove a linked wallet. |
| `GET` | `/api/profile/leaderboard` | Optional bearer | Global profile leaderboard. Query `type=score` or `type=referral`. |
| `GET` | `/api/profile/{walletAddress}` | Public | Public profile lookup. Optional `namespace`. |
| `GET` | `/api/profile/{walletAddress}/insights` | Public | Public profile insight summary. |

### Referrals

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/referral/public-stats` | Public | Public referral totals. |
| `POST` | `/api/referral/code` | Bearer | Get the default referral code and links for the current user. |
| `POST` | `/api/referral/apply` | Bearer | Apply a referral code to the authenticated wallet. |
| `POST` | `/api/referral/links` | Bearer | Create a named referral link. |
| `PUT` | `/api/referral/links/{referralLinkId}` | Bearer | Update a referral link. |
| `DELETE` | `/api/referral/links/{referralLinkId}` | Bearer | Delete a referral link. |
| `GET` | `/api/referral/activity` | Bearer | Paginated referral activity for the current user. |
| `GET` | `/api/referral/stats` | Bearer | Referral stats, links, totals, and payout summary. |

Use `referralCode` in quote lock and execute requests to attribute routed swaps.

### Rewards

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/rewards/campaigns` | Optional bearer | List public rewards campaigns. Optional `kind` or `kinds`. |
| `GET` | `/api/rewards/campaigns/current` | Optional bearer | Current active rewards campaign or `null`. |
| `GET` | `/api/rewards/campaigns/{slug}` | Optional bearer | Public campaign detail. |
| `GET` | `/api/rewards/campaigns/{slug}/leaderboard` | Optional bearer | Campaign leaderboard. |

Admin rewards endpoints require `x-api-key` and are strict-rate-limited:

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/rewards/admin/campaigns` | List campaigns for admin management. |
| `POST` | `/api/rewards/admin/campaigns` | Create a draft rewards campaign. |
| `PUT` | `/api/rewards/admin/campaigns/{campaignId}` | Update a rewards campaign. |
| `POST` | `/api/rewards/admin/campaigns/{campaignId}/activate` | Activate a campaign. |
| `POST` | `/api/rewards/admin/campaigns/{campaignId}/complete` | Complete a campaign. |
| `POST` | `/api/rewards/admin/campaigns/{campaignId}/archive` | Archive a completed campaign. |

### Notifications

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/notifications` | Optional bearer | List public notifications and authenticated private notifications. |
| `POST` | `/api/notifications/public` | API key | Publish a public notification. |
| `POST` | `/api/notifications/read-all` | Bearer | Mark all private notifications read. |
| `POST` | `/api/notifications/{notificationId}/read` | Bearer | Mark one notification read. |

### Referral Payout Admin

Referral payout admin endpoints require `x-api-key`.

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/referral/admin/payouts/preview` | Preview eligible payout items for a period. Query `periodStart` and `periodEnd`; `from` and `to` are aliases. |
| `GET` | `/api/referral/admin/payouts/batches` | List payout batches. |
| `POST` | `/api/referral/admin/payouts/batches` | Create a payout batch. |
| `GET` | `/api/referral/admin/payouts/batches/{batchId}` | Get a payout batch with payout items and earnings. |
| `POST` | `/api/referral/admin/payouts/batches/{batchId}/cancel` | Cancel an unpaid payout batch. |
| `POST` | `/api/referral/admin/payouts/items/{payoutItemId}/mark-paid` | Mark one payout item as paid after an external transfer. |

## CORS and Browser Integrations

The backend enforces an allowlist for browser origins. Server-to-server requests are not blocked by browser CORS, but frontend integrations should use an approved origin or proxy API requests through their own backend.

Allowed request headers include:

- `Content-Type`
- `Authorization`
- `X-API-Key`
- `x-api-key`

## Important Implementation Notes

- `GET /api/swap/chains` returns a top-level `chains` array.
- `GET /api/swap/sources` returns a top-level `sources` array.
- `GET /api/swap/assets` filters by `chain`, not `networkId`.
- Swaps between the same asset on the same chain return `400`.
- If no provider is available for a preview quote, `POST /api/quote` returns an empty array.
- Lock and execute require strict participant address validation. Send the real source `depositor` and destination `recipient`.
- Some provider responses are intentionally provider-specific. Preserve unknown fields in your client model where possible.
- Diamond execution is not active in the current production assumption. Do not require diamond contract addresses for integration.
