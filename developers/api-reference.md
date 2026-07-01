---
description: Production REST API for integrating 7.Exchange swap routing.
---

# API Reference

The 7.Exchange production API is ready for swap integrations.

```text
https://api.7.exchange/api/v1
```

All public swap integration endpoints are versioned under `/api/v1`. Use the endpoints below to discover assets and providers, request quotes, lock a selected route, execute the locked quote, and track resulting transactions. These swap integration endpoints do not require an API key unless an endpoint explicitly says otherwise.

## Versioning Policy

The current API version is `v1`. Every versioned response includes:

- `X-API-Version: 1`
- `X-Request-Id: req_...`

Non-breaking changes can ship within the same version. Examples: adding a field, adding a new enum value, adding an optional request parameter, or adding metadata.

Breaking changes require a new path version. Examples: removing a field, renaming a field, changing a field type, changing required request fields, changing enum casing, or changing response envelope shape.

Every changelog entry that affects the public API names the affected version.

## Response Conventions

- List endpoints return `{ "data": [ ... ], "pagination": { "page": 1, "perPage": 100, "total": 1, "hasMore": false } }`.
- Quote preview returns `{ "data": [ ... ] }` without pagination because it is a request-scoped route set, not a paginated resource.
- Single-resource and action endpoints return `{ "data": { ... } }`.
- Error responses always return `{ "error": { ... } }`.
- Rate-limited responses include `Retry-After`, `X-RateLimit-Limit`, `X-RateLimit-Remaining`, and `X-RateLimit-Reset`.
- JSON request and response fields use camelCase.
- Amounts are strings unless a field is explicitly a count or basis-point number.
- Timestamps are ISO 8601 strings.
- Provider-specific blocks such as `quote`, `fee`, and `exec` are always present as objects. They may be empty.
- Treat returned asset addresses as canonical for their chain. Compare EVM addresses case-insensitively unless you checksum them first.

## Integration Flow

1. Fetch chains with `GET /api/v1/swap/chains`.
2. Fetch assets with `GET /api/v1/swap/assets`.
3. Fetch routing providers with `GET /api/v1/swap/sources`.
4. Request routes with `POST /api/v1/quote`.
5. Lock the selected provider route with `POST /api/v1/quote/lock`.
6. Execute the locked quote with `POST /api/v1/quote/execute`.
7. Poll transaction status with `GET /api/v1/transaction/status`.
8. Send post-execute provider tracking metadata with `POST /api/v1/transaction/track` when the client learns a deposit hash, request ID, channel ID, or other provider-native tracking key.
9. Read transaction history with `GET /api/v1/transaction/history`.

If you are using the affiliate program, create an account in the 7.Exchange webapp, complete the affiliate flow, create or copy your referral link, and pass that link's code as `referralCode` in lock and execute requests.

## Request Rules

- Send JSON request bodies with `Content-Type: application/json`.
- Use the chain `key` returned by `GET /api/v1/swap/chains` as `srcChain` and `dstChain`.
- Use the asset `address` returned by `GET /api/v1/swap/assets` as `srcAddress` and `dstAddress`.
- Send `amount` as a positive human-readable token amount string.
- Send `slippage` in basis points. `100` means 1%.
- Send the real source wallet as `depositor` and the real destination wallet as `recipient` when requesting and locking quotes.
- Do not hardcode provider names; use the provider returned by the selected quote.
- Use `routeId` from quote preview when locking a selected route.
- Use `lockId` from lock when executing.

## Chains

```http
GET /api/v1/swap/chains
```

Lists active supported chains.

### Query Parameters

| Name | Type | Description |
|---|---|---|
| `page` | number | Page number. Defaults to `1`. |
| `perPage` | number | Results per page. Defaults to `100`, maximum `1000`. |
| `query` | string | Optional search across name, shortname, and key. |
| `type` | string | Optional network type filter: `EVM`, `COSMOS`, `UTXO`, or `OTHER`. |

### Example

```bash
curl -s "https://api.7.exchange/api/v1/swap/chains?query=ethereum"
```

### Response

```json
{
  "data": [
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
  "pagination": {
    "page": 1,
    "perPage": 100,
    "total": 1,
    "hasMore": false
  }
}
```

## Assets

```http
GET /api/v1/swap/assets
```

Lists supported swap assets. Use the returned `address` field in quote, lock, and execute requests.

### Query Parameters

| Name | Type | Description |
|---|---|---|
| `page` | number | Page number. Defaults to `1`. |
| `perPage` | number | Results per page. Defaults to `100`, maximum `1000`. |
| `query` | string | Optional search across name, symbol, and address. |
| `chain` | string | Optional chain key from `GET /api/v1/swap/chains`. |

### Example

```bash
curl -s "https://api.7.exchange/api/v1/swap/assets?chain=ethereum&query=usdc"
```

### Response

```json
{
  "data": [
    {
      "name": "USD Coin",
      "symbol": "USDC",
      "address": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
      "image": "https://...",
      "chain": "ethereum",
      "isNative": false
    }
  ],
  "pagination": {
    "page": 1,
    "perPage": 100,
    "total": 1,
    "hasMore": false
  }
}
```

## Sources

```http
GET /api/v1/swap/sources
```

Lists active routing providers.

### Query Parameters

| Name | Type | Description |
|---|---|---|
| `page` | number | Page number. Defaults to `1`. |
| `perPage` | number | Results per page. Defaults to `20`, maximum `300`. |
| `type` | string | Optional provider type: `BRIDGE`, `EXCHANGE`, or `SERVICE`. |

### Example

```bash
curl -s "https://api.7.exchange/api/v1/swap/sources?type=BRIDGE"
```

### Response

```json
{
  "data": [
    {
      "key": "ACROSS_PROTOCOL",
      "name": "Across",
      "image": "https://...",
      "type": "BRIDGE",
      "active": true
    }
  ],
  "pagination": {
    "page": 1,
    "perPage": 20,
    "total": 1,
    "hasMore": false
  }
}
```

## Get Quotes

```http
POST /api/v1/quote
```

Returns available routes for the requested pair and amount.

### Body

| Name | Required | Type | Description |
|---|---:|---|---|
| `srcChain` | Yes | string | Source chain key. |
| `srcAddress` | Yes | string | Source asset address from the assets endpoint. |
| `dstChain` | Yes | string | Destination chain key. |
| `dstAddress` | Yes | string | Destination asset address from the assets endpoint. |
| `amount` | Yes | string | Positive human-readable amount. |
| `slippage` | No | number | Slippage in basis points. |
| `depositor` | No | string | Source wallet address. Recommended for accurate routes. |
| `recipient` | No | string | Destination wallet address. Recommended for accurate routes. |
| `exclude` | No | string[] | Provider keys to exclude. |

### Example

```bash
curl -s "https://api.7.exchange/api/v1/quote" \
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

### Response

```json
{
  "data": [
    {
      "provider": "ACROSS_PROTOCOL",
      "routeId": "provider-route-id",
      "quote": {},
      "fee": {},
      "exec": {}
    }
  ]
}
```

Quote objects include provider-specific fields. Preserve the returned provider key and selected `routeId` until lock completes. If no route is available, this endpoint returns `200` with an empty `data` array.

## Lock Quote

```http
POST /api/v1/quote/lock
```

Locks one selected provider route and returns an execution payload.

### Body

Send the same fields used for `POST /api/v1/quote`, plus:

| Name | Required | Type | Description |
|---|---:|---|---|
| `provider` | Yes | string | Provider key from the selected quote. |
| `routeId` | No | string | Route ID from the selected quote. Recommended when multiple routes are returned. |
| `depositor` | Yes | string | Source wallet address. |
| `recipient` | Yes | string | Destination wallet address. |
| `referralCode` | No | string | Affiliate referral code from your 7.Exchange referral link. |

### Example

```bash
curl -s "https://api.7.exchange/api/v1/quote/lock" \
  -H "Content-Type: application/json" \
  -d '{
    "srcChain": "ethereum",
    "srcAddress": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "dstChain": "arbitrum",
    "dstAddress": "0xaf88d065e77c8cC2239327C5EDb3A432268e5831",
    "amount": "50",
    "slippage": 100,
    "provider": "ACROSS_PROTOCOL",
    "routeId": "provider-route-id",
    "depositor": "0xYourSourceWallet",
    "recipient": "0xYourDestinationWallet",
    "referralCode": "your-referral-code"
  }'
```

### Response

The response is provider-specific. It includes the locked `lockId` needed by execute and may include transaction data, approval data, or direct-send deposit instructions depending on the provider route.

```json
{
  "data": {
    "provider": "ACROSS_PROTOCOL",
    "routeId": "provider-route-id",
    "lockId": "locked-quote-id",
    "expiresAt": "2026-06-30T00:01:00.000Z",
    "ttlMs": 60000,
    "quote": {},
    "fee": {},
    "exec": {}
  }
}
```

## Execute Quote

```http
POST /api/v1/quote/execute
```

Executes the locked quote. Use the `lockId` returned by lock. The backend reads the locked source chain, destination chain, amount, slippage, provider, depositor, and recipient from the server-side lock record.

### Body

| Name | Required | Type | Description |
|---|---:|---|---|
| `lockId` | Yes | string | Locked quote ID returned by `POST /api/v1/quote/lock`. |
| `referralCode` | No | string | Affiliate referral code. If omitted, the code stored on the lock is used. |

### Example

```bash
curl -s "https://api.7.exchange/api/v1/quote/execute" \
  -H "Content-Type: application/json" \
  -d '{
    "lockId": "locked-quote-id",
    "referralCode": "your-referral-code"
  }'
```

### Response

The response is provider-specific. When the backend can record the swap, the response includes a `transaction` object containing public transaction data.

```json
{
  "data": {
    "provider": "ACROSS_PROTOCOL",
    "lockId": "locked-quote-id",
    "providerResult": {},
    "quote": {},
    "fee": {},
    "exec": {},
    "transaction": {
      "id": "public-transaction-id",
      "status": "PENDING"
    }
  }
}
```

## Transaction Status

```http
GET /api/v1/transaction/status
```

Returns the current public status for a transaction. Use this endpoint for polling after execute. The lookup accepts the public transaction `id` returned by execute, or any known transaction hash.

### Query Parameters

| Name | Type | Description |
|---|---|---|
| `transactionId` | string | Public transaction ID returned as `transaction.id`. Required when `hash` is omitted. |
| `hash` | string | Transaction hash lookup. Searches `hash`, `inboundHash`, and `outboundHash`. Required when `transactionId` is omitted. |

### Example

```bash
curl -s "https://api.7.exchange/api/v1/transaction/status?transactionId=public-transaction-id"
```

### Response

```json
{
  "data": {
    "transaction": {
      "id": "public-transaction-id",
      "hash": "0x...",
      "inboundHash": "0x...",
      "outboundHash": null,
      "provider": "ACROSS_PROTOCOL",
      "status": "PENDING",
      "srcAmount": "50",
      "dstAmount": "49.8",
      "walletAddress": "0xYourSourceWallet",
      "recipientWalletAddress": "0xYourDestinationWallet",
      "createdAt": "2026-06-30T00:00:00.000Z",
      "updatedAt": "2026-06-30T00:01:00.000Z",
      "fee": {},
      "srcAsset": {},
      "dstAsset": {}
    },
    "final": false
  }
}
```

`final` is `true` when status is `SUCCESS`, `FAILED`, or `REFUND`. `PENDING` means polling should continue.

## Track Transaction

```http
POST /api/v1/transaction/track
```

Starts or refreshes provider status tracking for a transaction that was already recorded by execute. Normal execute responses start tracking automatically when enough provider metadata is available. Use this endpoint when the client later learns provider-native data such as a deposit transaction hash, Chainflip vault swap transaction hash, LayerZero request/channel ID, TON deposit data, or another provider tracking key.

### Body

| Name | Required | Type | Description |
|---|---:|---|---|
| `transactionId` | Yes | string | Public transaction ID returned as `transaction.id`. |
| `txHash` | No | string | Source, deposit, or inbound transaction hash. TON external-message BoCs are accepted when provider metadata is sufficient to resolve them. |
| `provider` | No | string | Provider key. Defaults to the provider recorded on the transaction. |
| `chainId` | No | number | Provider chain ID when only one chain ID is needed. |
| `sourceChainId` | No | number | Source chain ID. |
| `destinationChainId` | No | number | Destination chain ID. |
| `originChainId` | No | number | Origin chain ID for provider APIs that use that name. |
| `depositId` | No | string | Provider-native deposit ID. |
| `depositAddress` | No | string | Provider deposit address, useful for direct-send or TON flows. |
| `depositMemo` | No | string | Provider deposit memo/tag when applicable. |
| `requestId` | No | string | Provider request ID. |
| `channelId` | No | string | Provider channel ID. |
| `providerQuoteId` | No | string | Provider-native quote ID. |
| `vaultSwapTxHash` | No | string | Chainflip vault swap transaction hash. |

### Example

```bash
curl -s "https://api.7.exchange/api/v1/transaction/track" \
  -H "Content-Type: application/json" \
  -d '{
    "transactionId": "public-transaction-id",
    "provider": "CHAINFLIP",
    "txHash": "0xDepositHash",
    "sourceChainId": 1,
    "destinationChainId": 42161,
    "vaultSwapTxHash": "0xVaultSwapHash"
  }'
```

### Response

```json
{
  "data": {
    "transaction": {
      "id": "public-transaction-id",
      "hash": "0xDepositHash",
      "inboundHash": "0xDepositHash",
      "provider": "CHAINFLIP",
      "status": "PENDING"
    },
    "final": false,
    "tracking": true
  }
}
```

After calling this endpoint, poll `GET /api/v1/transaction/status` with the same `transactionId`.

## Transaction History

```http
GET /api/v1/transaction/history
```

Returns public transaction history. Pass wallet addresses to scope results to specific users or wallets.

### Query Parameters

| Name | Type | Description |
|---|---|---|
| `addresses` | string or string[] | Wallet address filter. Can be repeated or comma-separated. |
| `page` | number | Page number. |
| `perPage` | number | Results per page. Defaults to `20`, maximum `50`. |
| `query` | string | Optional search term. |
| `referralCode` | string | Optional exact referral code filter. |
| `transactionId` | string | Optional public transaction ID filter. |
| `statuses` | string or string[] | `SUCCESS`, `PENDING`, `FAILED`, or `REFUND`. Can be repeated or comma-separated. |
| `finalizedOnly` | boolean | When `true`, only finalized transactions are returned. |

### Example

```bash
curl -s "https://api.7.exchange/api/v1/transaction/history?addresses=0xYourWallet&perPage=20"
```

### Response

```json
{
  "data": [
    {
      "id": "public-transaction-id",
      "status": "PENDING",
      "provider": "ACROSS_PROTOCOL",
      "createdAt": "2026-06-30T00:00:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "perPage": 20,
    "total": 1,
    "hasMore": false
  }
}
```

## Affiliate Referral Codes

There is no API-key generation flow for integrators.

To attribute swaps:

1. Create or sign in to a 7.Exchange account in the webapp.
2. Open the affiliate dashboard.
3. Create or copy a referral code.
4. Use the referral code as `referralCode` in `POST /api/v1/quote/lock` and `POST /api/v1/quote/execute`.

## Common Failures

All failures use one envelope:

```json
{
  "error": {
    "code": "INVALID_AMOUNT",
    "message": "amount must be a positive number string",
    "field": "amount",
    "requestId": "req_01H...",
    "details": {
      "errors": [
        {
          "code": "INVALID_AMOUNT",
          "field": "amount",
          "message": "amount must be a positive number string"
        }
      ]
    }
  }
}
```

Clients should branch on `error.code`, not `error.message`. Messages are human-facing and may change. When multiple validation errors are found, they are returned in `error.details.errors`.

| Failure | HTTP status | Code |
|---|---:|---|
| Missing or invalid required field | `400` | `VALIDATION_ERROR` |
| Invalid amount | `400` | `INVALID_AMOUNT` |
| Invalid slippage | `400` | `INVALID_SLIPPAGE` |
| Invalid participant address | `400` | `INVALID_ADDRESS` |
| Same-chain same-asset swap | `400` | `UNSUPPORTED_PAIR` |
| Missing `provider` on lock | `400` | `MISSING_PROVIDER` |
| Missing `lockId` on execute | `400` | `MISSING_LOCK_ID` |
| Expired or already-used lock | `409` | `QUOTE_EXPIRED` |
| Provider unavailable for the selected route | `409` or `503` | `PROVIDER_UNAVAILABLE` |
| Rate limited | `429` | `RATE_LIMITED` |
| Backend error | `500` | `INTERNAL_ERROR` |
| Upstream/provider error | `502` or `503` | `BAD_GATEWAY` or `SERVICE_UNAVAILABLE` |

If no route is available during quote preview, `POST /api/v1/quote` returns `200` with:

```json
{
  "data": []
}
```
