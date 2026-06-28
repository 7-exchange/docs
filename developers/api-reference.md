---
description: Production REST API for integrating 7.Exchange swap routing.
---

# API Reference

The 7.Exchange production API is ready for swap integrations.

```text
https://api.7.exchange
```

Use the endpoints below to discover assets, request quotes, lock a selected route, execute the locked quote, and read public transaction history. These swap integration endpoints do not require an API key.

## Integration Flow

1. Fetch chains with `GET /api/swap/chains`.
2. Fetch assets with `GET /api/swap/assets`.
3. Fetch providers with `GET /api/swap/sources`.
4. Request routes with `POST /api/quote`.
5. Lock the selected provider route with `POST /api/quote/lock`.
6. Execute the locked quote with `POST /api/quote/execute`.
7. Read transaction history with `GET /api/transaction/history`.

If you are using the affiliate program, create an account in the 7.Exchange webapp, complete the affiliate flow, create or copy your referral link, and pass that link's code as `referralCode` in lock and execute requests.

## Request Rules

- Send JSON request bodies with `Content-Type: application/json`.
- Use the chain `key` returned by `GET /api/swap/chains` as `srcChain` and `dstChain`.
- Use the asset `address` returned by `GET /api/swap/assets` as `srcAddress` and `dstAddress`.
- Send `amount` as a positive human-readable token amount string.
- Send `slippage` in basis points. `100` means 1%.
- Send the real source wallet as `depositor` and the real destination wallet as `recipient` when locking and executing.
- Do not hardcode provider names; use the provider returned by the selected quote.

## Chains

```http
GET /api/swap/chains
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
curl -s "https://api.7.exchange/api/swap/chains?query=ethereum"
```

### Response

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

## Assets

```http
GET /api/swap/assets
```

Lists supported swap assets. Use the returned `address` field in quote, lock, and execute requests.

### Query Parameters

| Name | Type | Description |
|---|---|---|
| `page` | number | Page number. Defaults to `1`. |
| `perPage` | number | Results per page. Defaults to `100`, maximum `1000`. |
| `query` | string | Optional search across name, symbol, and address. |
| `chain` | string | Optional chain key from `GET /api/swap/chains`. |

### Example

```bash
curl -s "https://api.7.exchange/api/swap/assets?chain=ethereum&query=usdc"
```

### Response

```json
{
  "assets": [
    {
      "name": "USD Coin",
      "symbol": "USDC",
      "address": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
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

## Sources

```http
GET /api/swap/sources
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
curl -s "https://api.7.exchange/api/swap/sources?type=BRIDGE"
```

### Response

```json
{
  "sources": [
    {
      "key": "ACROSS_PROTOCOL",
      "name": "Across",
      "image": "https://...",
      "type": "BRIDGE",
      "active": true
    }
  ],
  "page": 1,
  "perPage": 20,
  "total": 1
}
```

## Get Quotes

```http
POST /api/quote
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
curl -s "https://api.7.exchange/api/quote" \
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
[
  {
    "provider": "ACROSS_PROTOCOL",
    "quoteId": "provider-quote-id",
    "quote": {},
    "fee": {},
    "exec": {}
  }
]
```

Quote objects include provider-specific fields. Preserve the returned provider key and selected quote data until lock and execute complete.

## Lock Quote

```http
POST /api/quote/lock
```

Locks one selected provider route and returns an execution payload.

### Body

Send the same fields used for `POST /api/quote`, plus:

| Name | Required | Type | Description |
|---|---:|---|---|
| `provider` | Yes | string | Provider key from the selected quote. |
| `depositor` | Yes | string | Source wallet address. |
| `recipient` | Yes | string | Destination wallet address. |
| `referralCode` | No | string | Affiliate referral code from your 7.Exchange referral link. |

### Example

```bash
curl -s "https://api.7.exchange/api/quote/lock" \
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
    "referralCode": "your-referral-code"
  }'
```

### Response

The response is provider-specific. It includes the locked `quoteId` needed by execute and may include transaction data, approval data, or direct-send deposit instructions depending on the provider route.

```json
{
  "provider": "ACROSS_PROTOCOL",
  "quoteId": "locked-quote-id",
  "quote": {},
  "exec": {}
}
```

## Execute Quote

```http
POST /api/quote/execute
```

Executes the locked quote. Use the `quoteId` returned by lock.

### Body

Send the same fields used for lock, plus:

| Name | Required | Type | Description |
|---|---:|---|---|
| `quoteId` | Yes | string | Locked quote ID returned by `POST /api/quote/lock`. |

If using the affiliate program, pass the same `referralCode` used during lock.

### Example

```bash
curl -s "https://api.7.exchange/api/quote/execute" \
  -H "Content-Type: application/json" \
  -d '{
    "srcChain": "ethereum",
    "srcAddress": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "dstChain": "arbitrum",
    "dstAddress": "0xaf88d065e77c8cC2239327C5EDb3A432268e5831",
    "amount": "50",
    "slippage": 100,
    "provider": "ACROSS_PROTOCOL",
    "quoteId": "locked-quote-id",
    "depositor": "0xYourSourceWallet",
    "recipient": "0xYourDestinationWallet",
    "referralCode": "your-referral-code"
  }'
```

### Response

The response is provider-specific. When the backend can record the swap, the response includes a `transaction` object containing public transaction data.

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

## Transaction History

```http
GET /api/transaction/history
```

Returns public transaction history. Pass wallet addresses to scope results to specific users or wallets.

### Query Parameters

| Name | Type | Description |
|---|---|---|
| `addresses` | string or string[] | Wallet address filter. Can be repeated or comma-separated. |
| `page` | number | Page number. |
| `perPage` | number | Results per page. Maximum `50`. |
| `search` | string | Optional search term. |
| `statuses` | string or string[] | `SUCCESS`, `PENDING`, `FAILED`, or `REFUND`. Can be repeated or comma-separated. |
| `finalizedOnly` | boolean | When `true`, only finalized transactions are returned. |

### Example

```bash
curl -s "https://api.7.exchange/api/transaction/history?addresses=0xYourWallet&perPage=20"
```

### Response

```json
{
  "items": [
    {
      "id": "public-transaction-id",
      "status": "PENDING",
      "provider": "ACROSS_PROTOCOL"
    }
  ],
  "page": 1,
  "perPage": 20,
  "total": 1
}
```

## Affiliate Referral Codes

There is no API-key generation flow for integrators.

To attribute swaps:

1. Create or sign in to a 7.Exchange account in the webapp.
2. Open the affiliate dashboard.
3. Create or copy a referral link.
4. Use the referral code from that link as `referralCode` in `POST /api/quote/lock` and `POST /api/quote/execute`.

The webapp accepts referral links with `ref` or `referral` query parameters and sends the value as `referralCode` during swap requests.

## Common Failures

- Missing required fields return an error response.
- Invalid amounts, invalid slippage, unsupported same-chain same-asset swaps, unavailable providers, missing `provider` on lock, missing `quoteId` on execute, and invalid participant addresses return an error response.
- If no route is available during quote preview, `POST /api/quote` returns an empty array.
- Provider outages or unexpected backend errors return an error response.
