---
description: Answers to common questions about 7.Exchange.
---

# FAQ

## General

### What is 7.Exchange?

7.Exchange is a cross-chain DEX aggregator. It connects to multiple bridges, DEXs, and liquidity sources across chains and finds the swap route that delivers the best net output after accounting for gas, bridge fees, slippage, and MEV risk. It's non-custodial: your funds stay in your wallet until you sign a transaction.

### Do I need to create an account?

No. You can connect your wallet and start swapping immediately. Optionally, you can sign in (by signing a message in your wallet) to unlock additional features like transaction history, leaderboard tracking, and the referral program.

### Which chains are supported?

7.Exchange supports a wide range of EVM and non-EVM networks. The list of supported chains changes as providers are added. See the [Integrations page](https://7.exchange/integrations) for the current live list.

### Which wallets can I use?

7.Exchange supports wallets across EVM (MetaMask, Trust Wallet, Rabby, WalletConnect), Solana (Phantom, Solflare), Bitcoin (Xverse, Leather), Sui, and Tron ecosystems. See [Wallets](../supported-infrastructure/wallets.md) for the full list.

## Swapping

### How do I swap?

Connect your wallet, select source and destination tokens, review the available routes, and confirm. For a detailed walkthrough, see [Your First Swap](../getting-started/your-first-swap.md).

### How does 7.Exchange find the best rate?

The routing engine queries all integrated providers simultaneously, simulates each route end-to-end, and ranks them by net output the actual amount that arrives in your wallet after all fees. See [How Routing Works](../core-concepts/how-routing-works.md) for details.

### Can I swap between different chain types?

Yes. You can swap between EVM chains, between non-EVM chains, and between EVM and non-EVM chains (e.g., Ethereum to Solana). Cross-ecosystem swaps require wallets connected for both ecosystems.

### How long does a swap take?

Same-chain swaps typically confirm in seconds. Cross-chain swaps range from under a minute to several minutes, depending on the chains involved and network conditions. Some combinations with long finality times may take longer. The estimated time is always shown before you confirm.

### What if my swap is stuck?

Cross-chain swaps can take longer than expected during periods of network congestion. If your transaction shows "In progress" for an extended period, wait most transactions resolve on their own. If it remains unresolved, contact support with your Transaction ID or Quote ID. See [Transaction Tracking](../features/transaction-tracking.md) for more details.

### Can I cancel a swap?

No. Once you sign and broadcast a transaction, it is on-chain and irreversible. 7.Exchange cannot cancel, reverse, or modify on-chain transactions.

## Fees

### What fees does 7.Exchange charge?

A routing fee on each transaction, plus network gas fees, bridge fees, and DEX fees. All costs are displayed in the route preview before you confirm. See [Fees](../core-concepts/fees.md) for a full breakdown.

### Are there hidden fees?

No. Every cost routing fee, gas, bridge fee, and DEX fee is factored into the net output shown in the route preview. The amount you see is the amount you should expect to receive, subject to slippage.

### Does the referral program increase my costs?

No. Referral rewards are paid from the platform's routing fee. They do not increase the cost for the referred user.

## Security

### Is 7.Exchange custodial?

No. 7.Exchange is fully non-custodial. The platform never holds, controls, or has access to your funds. Every transaction is signed by you and executed directly through the underlying protocols.

### Will 7.Exchange ask for my private keys?

Never. Any request for private keys or seed phrases is fraudulent and not associated with 7.Exchange.

### What happens if a bridge gets exploited?

7.Exchange routes through third-party bridge protocols. If a bridge is exploited, transactions routed through that bridge may be affected. 7.Exchange cannot recover funds lost due to third-party protocol exploits. The provider-agnostic design means alternative routes through other bridges remain available.

### How do I report a security issue?

Email security findings to [contact@7.exchange](mailto:contact@7.exchange). Do not disclose vulnerabilities publicly before resolution. See [Bug Bounty Program](../security/bug-bounty-program.md) for details.

## Referral program

### How do I earn referral rewards?

Sign in, go to the Affiliate page, create a referral link, and share it. You earn a percentage of the routing fee on every swap made by users who signed up through your link. See [Referral Program](../features/referral-program.md) for details.

### Can I have multiple referral links?

Yes. You can create multiple links with different names to track performance across channels (e.g., one for Twitter, one for Discord).

### When are referral earnings paid?

Referral earnings are paid manually each week once your unclaimed referral balance reaches **$10**. Balances below $10 remain unclaimed and carry forward until they meet the minimum.
