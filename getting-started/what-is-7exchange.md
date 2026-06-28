---
description: What 7.Exchange is, how it works, and why it exists.
---

# What is 7.Exchange

7.Exchange is a cross-chain DEX aggregator. It connects to multiple bridges, DEXs, and liquidity sources across chains and finds the swap route that delivers the highest net output to you after accounting for gas, bridge fees, slippage, and MEV risk.

You interact with one interface. The routing engine handles the complexity of comparing providers, evaluating cross-chain paths, and building the transaction for you.

## The problem

Cross-chain trading today is fragmented. Liquidity is spread across hundreds of DEXs, bridges, and pools on different chains, each with different pricing, gas conditions, and execution risks.

To find the best rate for a cross-chain swap, you'd need to manually check multiple aggregators, compare bridge options, evaluate slippage on each leg, and manage approvals and transactions across networks. Most users either overpay or accept suboptimal routes because the alternative is too time-consuming.

## How 7.Exchange solves it

7.Exchange aggregates the entire cross-chain liquidity landscape into one execution layer. When you request a swap:

1. **Quote collection** The routing engine queries integrated bridges and liquidity sources simultaneously to gather available routes for your pair.
2. **Route evaluation** Each route is scored by net output: the final amount you receive after all fees, gas costs, slippage, and bridge charges are deducted. No misleading "before fees" numbers.
3. **Ranking** Routes are ranked and presented to you with the recommended option highlighted. You can also sort by speed or lowest fee.
4. **Execution** Once you confirm a route, 7.Exchange generates an authenticated execution payload. You sign and broadcast the transaction from your own wallet. The platform never takes custody of your funds.

## Key properties

**Non-custodial** Your assets stay in your wallet until you sign a transaction. 7.Exchange never holds, controls, or has access to your funds.

**Cross-chain native** Built from the ground up for multi-chain routing. Same-chain swaps and cross-chain swaps go through the same interface and the same routing logic.

**Provider-agnostic** 7.Exchange is not locked into a single bridge or DEX. The engine routes through whichever provider delivers the best outcome for each specific trade.

**Execution integrity** Each quote is unique, time-bound, and single-use. The execution payload is authenticated to prevent modification or replay. The route you approve is the route that executes.

## What you can do today

- **Cross-chain swaps** Swap any supported token across any supported chain in a single transaction.
- **Route comparison** View and compare multiple routes ranked by output, speed, or fee.
- **Custom settings** Set your own slippage tolerance and exclude specific providers or bridges.
- **Transaction tracking** Monitor the status of your swaps in real time.
- **Referral program** Earn crypto rewards by referring traders to the platform.

## What's coming next

7.Exchange is actively building toward a full cross-chain trading layer. Upcoming features include:

- **Token launch** with protocol-level utility including fee reduction and staking
- **Public API and SDK** for developers and partners
- **Limit orders** with automatic execution at your target price
- **Direct Swap** swap without connecting a wallet for supported pairs
- **Perpetual trading** aggregation across multiple on-chain venues
- **Diamond Router contracts** on-chain smart contract execution with third-party audits

For the full timeline, see the [Roadmap](../resources/roadmap.md).

## Architecture overview

At a high level, 7.Exchange sits between the user and the underlying DeFi infrastructure:

```
You (wallet) → 7.Exchange (routing engine) → Bridges, DEXs, LPs (execution)
```

The routing engine is the core of the platform. It handles provider communication, quote aggregation, path simulation, and transaction construction. The user-facing app and the future API/SDK are interfaces into this same engine.

7.Exchange does not run its own liquidity pools or bridge infrastructure. It aggregates existing protocols and optimizes routing across them.

{% hint style="info" %}
For a deeper look at how routing decisions are made, see [How Routing Works](../core-concepts/how-routing-works.md).
{% endhint %}
