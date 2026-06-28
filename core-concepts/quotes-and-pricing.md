---
description: How quotes work on 7.Exchange and what affects pricing.
---

# Quotes & Pricing

Every swap on 7.Exchange starts with a quote. This page explains how quotes are generated, what affects pricing, and how to read the route details.

## How quotes are generated

When you enter a swap pair and amount, the routing engine queries all integrated providers simultaneously. Each provider returns one or more routes with pricing based on current market conditions.

The engine then simulates each route end-to-end, calculates the net output after all costs, and ranks the results. This entire process typically takes a few seconds.

## What's included in a quote

Each quote displayed in the interface includes:

| Field | Description |
|---|---|
| **Output** | Estimated amount you'll receive in your destination wallet |
| **Fee** | Total cost across all legs of the route |
| **ETA** | Estimated time from execution to delivery |
| **Provider** | Which bridge, DEX, or service handles each step |
| **Route steps** | Breakdown of the path your swap takes |

The **Output** figure is a net number fees and costs are already deducted. What you see is what you should expect to receive, subject to slippage.

## What affects pricing

Several factors influence the rate you get on any given swap:

**Liquidity depth** Larger swaps may experience more slippage if the available liquidity on the best route can't absorb the full amount at the quoted price.

**Network gas costs** Gas prices fluctuate based on chain congestion. High gas periods reduce your net output.

**Bridge fees** Each bridge protocol charges its own fee for cross-chain transfers. These vary by bridge, chain pair, and transfer size.

**Token pair availability** Some token pairs have direct routes; others require multi-step paths through intermediate tokens, which adds cost.

**Market volatility** Rapid price movements between quote time and execution time can cause the final amount to differ from the estimate.

## Quote expiration

Quotes are time-sensitive. Each quote has a time-to-live (TTL) after which it expires and is automatically replaced with a fresh one.

The interface shows a countdown timer indicating when the current quotes will refresh. You can also manually refresh at any time by clicking the refresh button.

An expired quote cannot be executed. If you click Execute after a quote has expired, the system will fetch a new quote before proceeding.

## Indicative vs. final amounts

Quotes are **indicative estimates**, not guaranteed prices. The final amount you receive may differ from the quoted amount due to:

- Price movement between quote time and execution
- Slippage during on-chain execution
- Gas price changes
- Bridge fee adjustments

The slippage tolerance setting controls how much deviation you'll accept. If the actual output falls below your tolerance, the transaction will revert.

{% hint style="info" %}
For details on how to adjust slippage and other trade settings, see [Your First Swap](../getting-started/your-first-swap.md).
{% endhint %}
