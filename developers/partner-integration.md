---
description: Integrate 7.Exchange routing with referral attribution and partner-safe API patterns.
---

# Partner Integration

{% hint style="info" %}
Partners can integrate the live REST API today. Swap attribution is currently handled through referral codes passed into the quote lock and execute flow.
{% endhint %}

## Integration model

Partners integrate the same provider-first routing API used by the 7.Exchange app:

1. Discover supported infrastructure with `/api/swap/*`.
2. Request quotes with `POST /api/quote`.
3. Lock the chosen route with `POST /api/quote/lock`.
4. Execute with `POST /api/quote/execute`.
5. Track status with `/api/transaction/*`.
6. Pass an approved `referralCode` on lock and execute requests for attribution.

## Referral attribution

Include the partner referral code when locking and executing a selected route:

```json
{
  "srcChain": "ethereum",
  "srcAddress": "native",
  "dstChain": "arbitrum",
  "dstAddress": "0xaf88d065e77c8cC2239327C5EDb3A432268e5831",
  "amount": "0.05",
  "provider": "ACROSS_PROTOCOL",
  "depositor": "0xUserSourceWallet",
  "recipient": "0xUserDestinationWallet",
  "referralCode": "partner-code"
}
```

When a swap is recorded, the backend resolves the referral code to the referral link and stores attribution with the transaction.

{% hint style="warning" %}
Use the same `referralCode` on both `POST /api/quote/lock` and `POST /api/quote/execute`. If execute is called without the code, the recorded transaction may not carry partner attribution.
{% endhint %}

## Partner-facing endpoints

| Endpoint | Auth | Purpose |
|---|---|---|
| `GET /api/referral/public-stats` | Public | Public aggregate referral totals. |
| `POST /api/referral/code` | Bearer | Get the authenticated user's default code and links. |
| `POST /api/referral/links` | Bearer | Create a named referral link. |
| `PUT /api/referral/links/{referralLinkId}` | Bearer | Update link name or attached wallets. |
| `DELETE /api/referral/links/{referralLinkId}` | Bearer | Delete a referral link. |
| `GET /api/referral/activity` | Bearer | Paginated referral transaction activity. |
| `GET /api/referral/stats` | Bearer | Referral totals, link stats, and payout summary. |

Referral payout admin endpoints exist for internal operations and require an API key. Partners should not call payout admin endpoints from client applications.

## Non-custodial requirements

Partner integrations must remain non-custodial:

- Users sign source-chain transactions from their own wallets.
- Direct Send routes must clearly show the exact deposit address, memo or tag, token, chain, and amount.
- Partner systems should not take possession of user funds.
- API keys must stay on trusted partner backends only.

## Operational checklist

- Confirm your production origin is allowed if calling the API directly from a browser.
- Use the provider returned by the selected quote; do not hardcode provider names.
- Preserve provider-specific fields in logs for support and recovery.
- Track every executed swap by transaction ID or hash.
- Show a fresh quote if lock or execute returns an error.

## Contact

For commercial terms, approved referral codes, or browser-origin approval, contact [contact@7.exchange](mailto:contact@7.exchange).
