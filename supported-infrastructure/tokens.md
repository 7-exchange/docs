---
description: Supported tokens on 7.Exchange.
---

# Tokens

7.Exchange supports thousands of tokens across all integrated chains. Token availability depends on which tokens are supported by the underlying bridge and liquidity providers.

## Finding tokens

In the swap interface, use the token selector to search for tokens by name, symbol, or contract address. Tokens are organized by chain select a chain first, then browse or search for the token you need.

## Token metadata

Token information (names, symbols, logos, contract addresses) is sourced from third-party data providers and updated regularly. If a token's metadata appears incorrect or outdated, contact support.

## Custom tokens

If a token isn't listed in the default token list but you have its contract address, you can add it manually:

1. Open **Swap Settings**.
2. Go to **Custom Tokens**.
3. Click on **Add custom token**
4. Select the chain the token is on.
5. Enter the contract address.
6. Click **Import**.

The token will be added to your personal token list and available for selection in the swap interface.

{% hint style="warning" %}
Only import tokens from contract addresses you trust. Anyone can deploy a token contract verify the address through the project's official channels before importing.
{% endhint %}

## Token availability by route

Not every token can be swapped to every other token directly. Route availability depends on:

- Whether providers have liquidity for the pair
- Whether a bridgeable path exists between the source and destination chains for the tokens involved
- Whether intermediate swap steps are available on each chain

If no routes are available for a pair, the interface will display a message. Try adjusting the amount, or swapping to a more liquid intermediate token first.
