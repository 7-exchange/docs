---
description: Full development timeline from early infrastructure through advanced trading features.
---

# Roadmap

This roadmap covers the full development timeline of 7.Exchange from early infrastructure work through the current launch phase and into future product expansion. Timelines are targets priorities may shift based on market conditions, security considerations, and community feedback.

---

## Q4 2025 Infrastructure & Interface

- [x] **Frontend application**\
  Next.js-based interface with landing page, swap interface, profile, and affiliate pages. Responsive design across desktop and mobile.

- [x] **Wallet integrations**\
  Support for 40+ wallets across EVM (MetaMask, Trust, Rabby, WalletConnect), Solana (Phantom, Solflare), Bitcoin (Xverse, Leather), Sui, and Tron ecosystems.

- [x] **SIWE authentication**\
  Sign-In with Ethereum and equivalent standards for non-EVM chains. Wallet-based identity with no email/password accounts.

- [x] **Affiliate and scoring system**\
  Referral link creation, wallet binding, commission tracking, and real-time affiliate dashboard with activity feed and earnings breakdown.

- [x] **Deeper liquidity source integrations**\
  Expanded bridge and DEX provider coverage. Integration with Relay Protocol, Across Protocol, THORChain, and MayaChain for cross-chain routing.

- [x] **User profile system**\
  Multi-wallet profile management with custom avatars, display names, leaderboard ranking, activity charts, and per-chain/bridge/exchange usage statistics.

- [x] **Notification system**\
  Public and private notification feeds with read status tracking and real-time updates on swap activity and platform announcements.

---

## Q1 2026 Routing Engine & Core Systems

- [x] **Quote engine implementation**\
  Multi-provider quote aggregation with real-time path simulation. Each route scored by net output after gas, bridge fees, slippage, and MEV exposure. Quotes are unique, time-bound, and single-use.

- [x] **Cross-chain swap execution pipeline**\
  End-to-end execution flow: quote locking, authenticated payload construction, wallet signing, on-chain submission, and real-time status tracking across chains.

- [x] **Route comparison and ranking**\
  Multi-route display with sorting by highest output, fastest execution, and lowest fee. Route step visualization showing each bridge, swap, and service in the path.

- [x] **Custom swap settings**\
  User-configurable slippage tolerance, provider/bridge exclusion, and custom token imports via contract address.

- [x] **Transaction tracking system**\
  Real-time execution tracker with status progression (pending, in progress, completed, failed), transaction hash links, and provider attribution.

- [x] **Token and chain data infrastructure**\
  Automated token and chain metadata ingestion from third-party data providers. Searchable token lists organized by chain with logo, symbol, and contract address resolution.

- [x] **Leaderboard system**\
  Score-based and referral-based leaderboards ranking users by platform activity and affiliate performance.

---

## Q2 2026 Public Launch & Expansion

- [x] **MVP public launch**\
  Full platform live at [7.exchange](https://7.exchange). Cross-chain swap aggregation across 128+ chains, 10+ bridges, and 274+ liquidity sources through a single non-custodial interface. See the [Integrations page](https://7.exchange/integrations) for the live count.

- [ ] **Direct Swap via Chainflip**\
  Wallet-less swap execution using Chainflip's deposit address model. Users provide a receiving address and send funds from any source hardware wallet, CEX withdrawal, or any on-chain wallet without connecting to the platform.

- [ ] **Public REST API**\
  Authenticated API endpoints for programmatic access to the routing engine: quote retrieval, route computation, transaction construction, transaction status polling, and chain/token/provider discovery.

- [ ] **Tokenomics design**\
  Token model specification including utility mechanics (fee reduction, staking rewards, governance voting weight), supply structure, vesting schedules, and distribution framework.

- [ ] **Additional provider integrations**\
  New bridge and liquidity provider integrations to expand chain coverage, improve route diversity, and increase competition between providers for better user outcomes.

- [ ] **Diamond Router smart contracts bug bounty**\
  Pre-deployment security program for the modular on-chain routing contracts (EIP-2535 Diamond standard). Open to external security researchers with structured reward tiers based on severity.

---

## Q3 2026 Token Launch & Developer Tools

- [ ] **Token contract audit**\
  Independent third-party security audit of the token smart contract. Audit report published publicly prior to token launch.

- [ ] **Token launch**\
  Native 7.Exchange token live on-chain with protocol-level utility: reduced routing fees for holders, staking with yield from protocol revenue, and governance participation for protocol parameter decisions.

- [ ] **TypeScript SDK**\
  Developer SDK wrapping the REST API for streamlined integration. Includes quote fetching, route comparison, transaction building, status callbacks, and configurable routing parameters. Designed for wallets, trading terminals, portfolio dApps, and institutional desks.

- [ ] **Embeddable swap widget**\
  Drop-in React component that partners and dApps can embed directly into their products. Customizable theming, token/chain restrictions, and affiliate fee attribution built in.

- [ ] **Limit orders**\
  On-chain limit order engine. Users set a target price and the order executes automatically when market conditions are met. Orders are held off-chain with authenticated execution no continuous wallet signing required.

- [ ] **Expanded provider integrations**\
  Continued broadening of bridge, DEX, and liquidity source coverage. LayerZero transfer integration. Provider performance monitoring with automatic route quality scoring.

---

## Q4 2026 Advanced Trading

- [ ] **Perpetual trading aggregation**\
  Unified interface for aggregated perpetual DEX trading. Open, manage, and close leveraged positions across multiple on-chain perp venues through the 7.Exchange routing engine. Cross-chain margin routing without manual network switching.

- [ ] **Expanded provider integrations**\
  Further broadening of chain coverage and liquidity depth. New perp venue integrations. Provider selection refined by historical execution quality, fill rates, and reliability metrics.

---

## Beyond 2026

The following items are on the longer-term roadmap. Timing and prioritization will be guided by community governance once the token and governance framework are active.

- [ ] **Diamond Router contract deployment**\
  Mainnet deployment of the audited on-chain routing contracts. Hybrid execution model: smart contract routing where deployed, provider-API fallback for maximum coverage.

- [ ] **On-chain fee distribution**\
  Protocol revenue accrual and distribution through the Diamond Router contracts, integrated with the native token staking mechanism.

- [ ] **Governance framework**\
  On-chain governance for protocol parameters, provider inclusion/exclusion, fee structures, and treasury allocation.

- [ ] **Extended Direct Swap coverage**\
  Deposit-based wallet-less swaps expanded beyond Chainflip to additional providers and chain pairs.

- [ ] **Advanced order types**\
  DCA (dollar-cost averaging), TWAP (time-weighted average price), and conditional execution strategies.

- [ ] **Cross-chain portfolio aggregation**\
  Unified portfolio view across all connected wallets and chains with balance tracking, performance analytics, and rebalancing tools.

- [ ] **Ecosystem plugins**\
  Embeddable swap and trading widgets for third-party dApps, websites, and wallet extensions.

---

{% hint style="info" %}
This roadmap is updated as milestones are reached. For real-time updates, follow [7.Exchange on X](https://x.com/7exchangeDeFi) or join the [Discord](https://discord.gg/your-server).
{% endhint %}