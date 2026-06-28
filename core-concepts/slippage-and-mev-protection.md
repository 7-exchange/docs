---
description: How slippage works and how 7.Exchange protects against MEV.
---

# Slippage & MEV Protection

Slippage and MEV (Maximal Extractable Value) are two risks that can reduce the amount you receive on a swap. This page explains what they are and how 7.Exchange handles them.

## What is slippage

Slippage is the difference between the quoted price and the actual execution price. It occurs because market conditions can change between the moment you see a quote and the moment your transaction is confirmed on-chain.

Common causes of slippage:

- **Price movement** The token price shifts while your transaction is pending in the mempool.
- **Low liquidity** Your trade size is large relative to the available liquidity in the pool, moving the price as it fills.
- **Network delays** Slow block times or congestion mean your transaction takes longer to confirm, during which the price changes.

Slippage can work in your favor (positive slippage, where you receive more than quoted) or against you (negative slippage, where you receive less).

## Slippage tolerance

7.Exchange lets you set a **slippage tolerance** the maximum negative deviation you'll accept from the quoted output.

If the actual execution price falls outside your tolerance, the transaction reverts and your funds are returned. You don't receive a partial or worse-than-expected fill.

To adjust your slippage tolerance, open **Swap Settings** and change the percentage. A lower tolerance protects you more aggressively but increases the chance of reverts on volatile pairs. A higher tolerance accepts more deviation but reduces revert risk.

{% hint style="info" %}
The default slippage tolerance works well for most swaps. Consider increasing it for low-liquidity tokens or during high-volatility periods.
{% endhint %}

## What is MEV

MEV refers to profit that can be extracted from users by reordering, inserting, or censoring transactions within a block. The most common form affecting swaps is a **sandwich attack**:

1. An attacker sees your pending swap in the mempool.
2. They place a buy order before yours, pushing the price up.
3. Your swap executes at the inflated price.
4. The attacker sells immediately after, pocketing the difference.

This results in you receiving fewer tokens than you should have.

## How 7.Exchange mitigates MEV

7.Exchange reduces MEV exposure through several mechanisms in the routing and execution pipeline:

**Quote-bound execution** Each execution payload is tied to a specific quote with defined parameters. The transaction is structured to execute within the quoted bounds or revert.

**Provider-level protections** Many of the integrated bridges and DEXs have their own MEV protection mechanisms, including private transaction submission and MEV-resistant pool designs. The routing engine factors these into route selection.

**Slippage enforcement** Your slippage tolerance acts as a hard limit. Even if an attacker manipulates the price, your transaction will revert if the output falls below your threshold.

**Route evaluation** The routing engine accounts for MEV risk when scoring routes. Routes through MEV-resistant protocols or private submission channels may be ranked higher when the risk is significant.

## Limitations

No system can eliminate MEV entirely. Transactions on public blockchains are visible in the mempool before confirmation, and sophisticated actors can exploit this. The protections described above reduce risk but do not guarantee immunity.

For large swaps on chains with high MEV activity, consider splitting your trade into smaller amounts or using routes through providers with built-in private transaction support.
