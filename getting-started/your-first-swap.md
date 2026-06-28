---
description: Walk through your first cross-chain swap on 7.Exchange.
---

# Your First Swap

This guide walks through a complete swap from start to finish.

## 1. Connect your wallet

If you haven't already, connect your wallet by clicking **Connect Wallet** in the navigation bar. See [Connect Your Wallet](connect-your-wallet.md) for details.

{% hint style="info" %}
Connecting a wallet is not required for all routes. Routes tagged with **Direct Send** let you swap without connecting a wallet at all, you'll manually enter your addresses and send funds directly. See the [Direct Send](#direct-send) section below for details.
{% endhint %}

## 2. Select your source token

On the swap interface, the **From** field is where you select what you're swapping away.

- Click the token selector to open the token and chain picker.
- Select the **chain** your funds are on.
- Select the **token** you want to swap.
- Enter the **amount** you want to swap, or click **Max** to use your full balance.

## 3. Select your destination token

In the **To** field, select the token and chain you want to receive.

This can be on the same chain (a simple swap) or a different chain (a cross-chain swap). The routing engine handles both the same way.

## 4. Review routes

Once both tokens and an amount are set, 7.Exchange fetches quotes from all available providers and displays the results.

Each route shows:

- **Output** The estimated amount you'll receive
- **Fee** Total cost including gas, bridge fees, and routing fee
- **ETA** Estimated time to completion
- **Provider** Which bridge or DEX is used for each step

The recommended route is highlighted by default. You can sort routes by **Highest Output**, **Fastest**, or **Lowest Fee** depending on your priority.

Routes include tags like **Direct Send**, **Recommended**, **Fastest**, and **Lowest Fee** to help you compare at a glance.

{% hint style="info" %}
Quotes refresh automatically. The countdown timer shows when the next refresh occurs. You can also manually refresh at any time.
{% endhint %}

## 5. Adjust settings (optional)

Click the settings icon to customize:

- **Slippage tolerance** The maximum price deviation you'll accept. Default is usually sufficient, but you can adjust for volatile tokens.

## 6. Choose swap participants

Before confirming a route, you need to set your **source** and **destination** wallet addresses: the addresses funds will be sent from and delivered to.

If you have multiple wallets connected, you can select which one to use for each side. You can also manually type or paste any wallet address into the input fields instead of selecting a connected wallet. This is useful if you want to receive funds at a different address or send from a wallet that isn't directly connected to the platform.

For most routes, the **source address** is determined by your connected wallet. However, for routes tagged with **Direct Send**, you can manually enter a source address as well, since you'll be sending funds yourself from any wallet you choose.

## 7. Confirm and execute

Once you've selected a route and set your swap participants:

1. Click **Swap** to lock the quote.
2. Review the **Quote Preview**: verify the tokens, amounts, source and destination addresses, and route details.
3. Click **Execute Quote**.
4. Your wallet will prompt you to sign the transaction. Review and approve it.

After signing, the execution tracker appears showing real-time progress of your swap.

## Direct Send

Some routes are tagged with **Direct Send**, which means they do not require connecting a wallet at all. Instead, you manually send funds to a provided address. This is useful if you prefer to send from a hardware wallet, a custodial exchange, or any wallet that isn't directly supported by the platform.

You can identify these routes by the **Direct Send** tag displayed alongside the route in the results list.

### How Direct Send works

1. Select your source and destination tokens and enter your amount as usual.
2. Choose a route tagged with **Direct Send** and click **Swap**.
3. In the swap participants step, enter your **source wallet address** (the wallet you'll be sending from) and your **destination wallet address** (where you want to receive the swapped funds). Since Direct Send doesn't require a connected wallet, you can type both addresses manually.
4. Review the **Quote Preview** and click **Execute Quote**.
5. The screen will display a **deposit address** along with a **QR code**. This is the address you need to send your funds to.
6. Open your own wallet (hardware wallet, exchange, mobile wallet, or any other wallet) and send **exactly** the amount shown on screen to the deposit address. You can scan the QR code or copy the address manually.
7. Once the platform detects your deposit, the swap will execute automatically and the funds will be delivered to your destination address.

{% hint style="danger" %}
**Direct Send transactions are irreversible.** You must send the **exact amount** displayed on screen to the **exact address** provided. Sending the wrong amount, sending to the wrong address, or sending the wrong token will result in a permanent loss of funds. 7.Exchange cannot recover, refund, or reverse Direct Send transactions. Double-check everything before you send.
{% endhint %}

### Direct Send tips

- **Copy the address directly** from the platform rather than typing it by hand. A single wrong character means lost funds.
- **Do not close the page** or navigate away until your deposit has been detected and the swap is processing.
- **Send in a single transaction.** Do not split the amount across multiple sends.
- **Verify the network.** Make sure you are sending on the correct chain. Sending tokens on the wrong network will result in lost funds.

## 8. Track your transaction

The execution tracker shows the current status of your swap:

| Status | Meaning |
|---|---|
| **Pending** | Transaction submitted, waiting for confirmation |
| **In progress** | Swap is executing across chains |
| **Completed** | Funds delivered to your destination wallet |
| **Failed** | Transaction did not complete (see [FAQ](../resources/faq.md) for troubleshooting) |

For cross-chain swaps, execution may take anywhere from a few seconds to several minutes or even hours depending on the chains involved and network conditions.

## Tips

- **Start with a small amount** if this is your first time using the platform or swapping on an unfamiliar chain.
- **Check your destination wallet** if you're swapping to a different chain ecosystem (e.g., EVM to Solana), make sure the correct destination wallet is connected or the correct address is entered.
- **Quotes are time-sensitive** if a quote expires before you execute, the system will fetch a fresh one automatically.