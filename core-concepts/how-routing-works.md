---
description: How 7.Exchange finds the best swap path across chains.
---

# How Routing Works

The routing engine is the core of 7.Exchange. It determines which path your swap takes across chains, bridges, and liquidity sources to deliver the best possible outcome.

## What the engine does

When you enter a swap, the routing engine runs through four stages:

### 1. Quote collection

The engine queries all integrated providers simultaneously with your swap parameters source token, destination token, amount, and any preferences you've set (slippage tolerance, excluded providers). Each provider returns one or more possible routes.

### 2. Path simulation

Each returned route is simulated end-to-end. The engine doesn't just compare raw quotes it models the full execution path and calculates what you'd actually receive after:

- Network gas fees on source and destination chains
- Bridge or cross-chain transport fees
- DEX swap fees and liquidity provider fees
- Estimated slippage based on current pool depth
- MEV exposure risk on supported chains

### 3. Net-output ranking

Routes are ranked by **net output** the final amount delivered to your wallet after all costs are deducted. This is the number that matters, and it's what 7.Exchange optimizes for by default.

You can also sort by fastest execution time or lowest total fee, depending on your priority.

### 4. Execution payload construction

When you select and confirm a route, the engine builds an authenticated execution payload. This payload contains all the instructions needed to execute the swap through the selected provider. It's sent to your wallet for signing.

## Why net-output ranking matters

Most aggregators show you a gross quote the headline number before fees are subtracted. This can be misleading. A route that quotes 1,000 USDC but charges 15 USDC in bridge fees and 5 USDC in gas is worse than a route quoting 990 USDC with 2 USDC in total fees.

7.Exchange compares routes by what actually arrives in your wallet. Every cost is factored in before ranking.

## Multi-step routes

Some cross-chain swaps require multiple steps. For example, swapping Token A on Chain X to Token B on Chain Y might involve:

1. Swapping Token A to a bridgeable asset on Chain X
2. Bridging that asset from Chain X to Chain Y
3. Swapping the bridged asset to Token B on Chain Y

The routing engine evaluates these multi-step paths as a single unit. You see the full route breakdown in the interface, but you execute it with a single confirmation.

## Provider selection

7.Exchange is provider-agnostic. The engine doesn't favor any single bridge or DEX. For every swap, all available providers are queried and the best result wins.

Different providers may be optimal for different pairs, amounts, or chain combinations. A provider that gives the best rate for a 100 USDC swap may not be the best for a 50,000 USDC swap due to liquidity depth. The engine handles this automatically.

## Quote freshness

Cross-chain quotes are volatile. Prices, liquidity, and gas costs change constantly. To protect you from executing on stale data:

- Each quote is assigned a **unique identifier**
- Quotes have a **time-to-live** and expire automatically
- Each quote can only be **used once** no replay
- Quotes **refresh automatically** in the interface on a countdown timer

If a quote expires before you execute, the system fetches a fresh one.

## What the engine does not do

- It does not hold or custody your funds at any point
- It does not execute transactions on your behalf you sign every transaction
- It does not run its own liquidity pools or bridge infrastructure
- It does not guarantee a specific output amount quotes are estimates based on current market conditions

{% hint style="info" %}
For details on how your transaction stays protected after you confirm a route, see [Cross-Chain Execution](cross-chain-execution.md).
{% endhint %}
