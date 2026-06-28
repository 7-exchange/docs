---
description: Monitor your swaps in real time.
---

# Transaction Tracking

Every swap on 7.Exchange is tracked from execution to delivery. The execution tracker gives you real-time visibility into where your transaction is in the pipeline.

## How tracking works

After you sign and submit a transaction, the execution tracker appears in the swap interface. It monitors your transaction across all chains involved in the route and updates the status as each step completes.

## Transaction statuses

| Status | Meaning |
|---|---|
| **Pending** | Transaction has been submitted and is waiting for on-chain confirmation. |
| **In progress** | The swap is actively executing. For cross-chain swaps, this means the bridge transfer is in progress. |
| **Completed** | Tokens have been delivered to your destination wallet. |
| **Failed** | The transaction did not complete. Your funds are typically returned to your source wallet, depending on at which step the failure occurred. |

## Viewing transaction details

Each tracked transaction shows:

- **Transaction ID** Your unique swap identifier
- **Quote ID** The quote this transaction was executed against
- **Provider** Which bridge or DEX handled the swap
- **Source and destination** Tokens, chains, and amounts
- **Hash** On-chain transaction hash(es) for each leg of the route
- **Status** Current execution state with timestamps

## Transaction history

If you've signed in with your wallet, all your past transactions are saved to your profile. You can view your full transaction history from the **Trade History** section on the swap page or from your **Profile**.

The history shows the last 20 transactions with details on date, tokens, amounts, status, and provider.

## Troubleshooting stuck transactions

If a transaction shows **In progress** for an extended period:

1. **Wait** Cross-chain transactions can take several minutes to hours depending on the chains involved and network congestion. This is normal.
2. **Check the provider** Some bridge protocols have their own status tracking. The provider name is shown in your transaction details.
3. **Contact support** If the transaction remains unresolved after a reasonable period, reach out to support with your Transaction ID or Quote ID.

{% hint style="info" %}
7.Exchange cannot cancel, reverse, or modify on-chain transactions. Resolution of stuck cross-chain transfers depends on the underlying bridge provider's recovery mechanisms.
{% endhint %}
