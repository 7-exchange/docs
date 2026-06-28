---
description: How to connect your wallet and sign in to 7.Exchange.
---

# Connect Your Wallet

7.Exchange uses wallet-based authentication. There are no accounts, emails, or passwords. Your wallet address is your identity on the platform.

## Supported wallet types

7.Exchange supports wallets across multiple ecosystems:

| Ecosystem | Examples |
|---|---|
| EVM | MetaMask, Trust Wallet, Rabby, Coinbase Wallet, WalletConnect-compatible wallets |
| Solana | Phantom, Solflare |
| Bitcoin | Xverse, Leather |
| Sui | Sui Wallet |
| Tron | TronLink |

For a full list of supported wallets, see [Wallets](../supported-infrastructure/wallets.md).

## Connecting your wallet

1. Go to [app](https://7.exchange/swap).
2. Click **Connect** in the top navigation.
3. Select ecosystem.
4. Select your wallet provider from the list.
5. Approve the connection request in your wallet.

{% hint style="info" %}
By selecting **WalletConnect** you can connect multiple namespaces at once
{% endhint %}

Once connected, your wallet balance appears in the navigation bar. You can view connected wallet addresses by clicking on it.

## Signing in (optional)

Connecting your wallet is enough to start swapping. However, signing in with your wallet unlocks additional features:

- **Profile**: Custom display name and avatar
- **Transaction history**: View all past swaps
- **Leaderboard**: Track your ranking
- **Referral program**: Create and manage referral links
- **Notifications**: Receive updates on your activity

To sign in, click **account icon** and select **Sign in to view profil**. You'll be asked to sign a message in your wallet. This signature verifies ownership of the address it does not grant 7.Exchange access to your funds or approval to execute transactions.

## Multiple wallets

You can connect wallets from different ecosystems simultaneously. This is useful when swapping between chains that use different wallet types for example, swapping from an EVM token to a Solana token.

To connect an additional wallet, click on **add wallet icon**.

{% hint style="info" %}
You can bind and un-bind multiple wallet addresses to your profile and affiliate links.
{% endhint %}

## Disconnecting

To disconnect your wallet, open the wallet menu and select **Disconnect**. To disconnect all wallets at once, select **Disconnect All**.

Disconnecting removes the wallet connection from the 7.Exchange interface. It does not affect your on-chain assets or any pending transactions.

{% hint style="warning" %}
7.Exchange will never ask for your private keys or seed phrase. Any request for this information is fraudulent.
{% endhint %}
