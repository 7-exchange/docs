---
description: Bridge protocols and liquidity sources integrated into 7.Exchange.
---

# Bridges & Liquidity Sources

7.Exchange aggregates liquidity and routing from multiple bridge protocols, DEXs, and on-chain services. The routing engine queries all integrated providers for every swap and selects the best route by net output.

## Bridge providers

Bridge providers handle the cross-chain transport layer moving assets between blockchain networks. Each bridge has its own mechanism, security model, and supported chain pairs.

7.Exchange integrates with multiple bridge protocols to maximize route coverage and redundancy. For any given cross-chain swap, multiple bridges may offer routes, and the engine ranks them by final output.

## DEXs and liquidity sources

For on-chain swaps (within a single chain or as part of a multi-step cross-chain route), 7.Exchange routes through integrated DEXs and liquidity pools. This includes automated market makers (AMMs), order book DEXs, and other on-chain liquidity venues.

## Provider selection

**7.Exchange is provider-agnostic**. The routing engine does not favor any single provider. For each swap, all available providers are queried and the best result is selected based on net output, speed, or fee depending on your sort preference.

Different providers may be optimal for different swaps depending on the token pair, amount, and chain combination.

## Live integrations

The current list of all integrated bridges, DEXs, liquidity sources, and services is available on the [Integrations page](https://7.exchange/#integrations) on the main site. You can filter by type (bridge, DEX, liquidity, service) to explore what's available.

## Excluding providers

If you prefer not to route through a specific provider, you can exclude it in **Swap Settings**:

1. Open Swap Settings.
2. Go to **Providers** or **Bridges**.
3. Search for the provider you want to exclude.
4. Toggle it off.

Excluded providers will not be used in any routes for your swaps. You can re-enable them at any time.

{% hint style="info" %}
Excluding providers may reduce the number of available routes or result in worse pricing. Only exclude providers if you have a specific reason.
{% endhint %}
