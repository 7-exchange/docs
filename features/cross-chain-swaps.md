---
description: Swap tokens across chains through a single interface.
---

# Cross-Chain Swaps

Cross-chain swaps are the core feature of 7.Exchange. Swap any supported token on any supported chain to any other supported token on any other chain, all from one interface.

## How it works

A cross-chain swap on 7.Exchange combines bridging and swapping into a single flow. Instead of manually bridging an asset to the destination chain and then swapping it on a local DEX, the routing engine handles the entire path.

Depending on the pair, a cross-chain swap may involve:

- A direct bridge transfer (if the same token is supported on both chains)
- A swap on the source chain, bridge, then delivery on the destination chain
- A bridge transfer followed by a swap on the destination chain
- A combination of all three (swap, bridge, swap)

You don't need to think about these steps. The routing engine evaluates all possible paths and presents the best options ranked by net output.

## Same-chain swaps

7.Exchange also supports same-chain swaps. If your source and destination tokens are on the same chain, the engine routes through integrated DEXs and liquidity sources on that chain.

Same-chain and cross-chain swaps use the same interface and the same routing logic.

## Route selection

Once you set your source and destination tokens and enter an amount, 7.Exchange fetches quotes from all available providers and displays them as routes. Each route shows the estimated output, total fees, estimated completion time, and the provider handling each step.

Routes are tagged to help you compare at a glance. Tags include **Recommended**, **Fastest**, **Lowest Fee**, and **Direct Send**. You can sort routes by highest output, fastest execution, or lowest fee depending on your priority.

Quotes refresh automatically on a countdown timer, and you can manually refresh at any time.

## Swap participants

Before confirming a route, you choose your swap participants: the source and destination wallet addresses.

If you have multiple wallets connected, you can select which wallet to use for each side. You can also manually type or paste any wallet address into the input fields instead of selecting a connected wallet. This is useful when you want to send from or receive at an address that isn't connected to the platform.

For most routes, the source address must match a connected wallet since the platform needs your wallet to sign the transaction. For routes tagged with **Direct Send**, you can enter any source address manually because you will be sending the funds yourself outside the platform.

The destination address determines where your swapped tokens are delivered. By default this is your connected wallet on the destination chain, but you can change it to any valid address.

{% hint style="warning" %}
Double-check both the source and destination addresses and their chains before confirming. Cross-chain transactions are irreversible. Tokens sent to an incorrect address cannot be recovered.
{% endhint %}

## Direct Send

Routes tagged with **Direct Send** do not require a connected wallet. Instead of signing a transaction through the platform, you manually send funds to a deposit address provided after you execute the quote.

Direct Send is useful when you want to swap from a hardware wallet, a custodial exchange, or any wallet that the platform does not support for direct connection.

After you execute a Direct Send route, the platform displays a deposit address and a QR code. Open your own wallet and send exactly the amount shown on screen to that address. You can scan the QR code or copy the address. Once the platform detects your deposit, the swap executes automatically and the funds are delivered to your destination address.

{% hint style="danger" %}
**Direct Send transactions are irreversible and cannot be corrected after the fact.** You must send the exact amount displayed, to the exact address provided, using the correct token on the correct network. Sending the wrong amount, the wrong token, or sending on the wrong chain will result in permanent loss of funds. 7.Exchange cannot recover, refund, or reverse any Direct Send transaction. Double-check everything before you send.
{% endhint %}

## Execution time

Same-chain swaps typically confirm within seconds. Cross-chain swaps take longer because they depend on:

- Block finality requirements on the source chain
- Bridge protocol processing time
- Block confirmation on the destination chain

Most cross-chain swaps complete within a few minutes. Some combinations (particularly those involving chains with long finality times) may take longer. The estimated time is always shown in the route details before you confirm.

## Slippage

You can adjust your slippage tolerance in the settings before confirming a swap. Slippage tolerance sets the maximum price deviation you are willing to accept between the quoted output and the final executed amount. The default value is usually sufficient, but you may want to adjust it for volatile tokens or low-liquidity pairs.

## Limits

7.Exchange does not impose minimum or maximum swap amounts. However, practical limits exist:

- **Small swaps** may not be cost-effective if gas and bridge fees consume a large portion of the swap amount.
- **Large swaps** may experience higher slippage if the available liquidity on the best route is insufficient for the full amount.

The route preview always shows the net output so you can judge whether the trade is worth executing.