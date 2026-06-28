---
description: Supported blockchain networks on 7.Exchange.
---

# Chains & Networks

7.Exchange supports swaps across a wide range of EVM-compatible and non-EVM blockchain networks.

## Supported ecosystems

| Ecosystem | Examples |
|---|---|
| **EVM** | Ethereum, BNB Chain, Polygon, Arbitrum, Optimism, Avalanche, Base, Fantom, zkSync, Linea, Scroll, and many more |
| **Solana** | Solana mainnet |
| **Bitcoin** | Bitcoin mainnet |
| **Cosmos** | Cosmos Hub and IBC-connected chains |
| **Other** | Sui, Tron, and additional non-EVM networks |

## Live chain list

The list of supported chains changes as providers add or deprecate network support. For the current live list, visit the [Integrations page](https://7.exchange/#integrations) on the main site, where you can filter by chain, bridge, DEX, and wallet.

## How chain support works

7.Exchange does not connect to chains directly. Chain support comes from the integrated bridge and liquidity providers. When a provider adds support for a new chain, that chain becomes available on 7.Exchange automatically as part of the next integration update.

This means:

- Not every token on every chain is available for swapping. Availability depends on which tokens the integrated providers support on each chain.
- Some chains may have limited route options if only one provider supports them.
- New chains are added regularly as the provider ecosystem expands.

## Chain-specific considerations

**Gas tokens**: Each chain requires its native token for gas. Make sure you have enough gas on the destination chain to interact with received tokens after the swap.

**Finality times**: Different chains have different block finality times. Cross-chain swaps involving chains with longer finality (e.g., Bitcoin, Ethereum mainnet) will take longer to complete.

**Wallet compatibility**: Not all wallets support all chains. Make sure your wallet supports the destination chain before swapping. See [Wallets](wallets.md) for supported wallet types by ecosystem.
