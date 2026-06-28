---
description: How 7.Exchange handles security.
---

# Security Model

7.Exchange is designed with a non-custodial, trust-minimized architecture. This page outlines the security properties of the platform.

## Non-custodial

7.Exchange never holds, controls, or has access to your funds. Your assets remain in your wallet until you sign a transaction. The platform generates transaction payloads you decide whether to sign and broadcast them.

There is no deposit step, no account balance, and no withdrawal process. Every trade is a direct wallet-to-protocol transaction that you authorize.

## Wallet authentication

7.Exchange uses Sign-In with Ethereum (SIWE) and equivalent standards for other ecosystems to verify wallet ownership. Signing in involves signing a message not a transaction. This signature proves you own the address without granting any on-chain permissions.

Your wallet connection allows 7.Exchange to:

- Read your wallet address
- Request transaction signatures (which you must approve individually)

It does not allow 7.Exchange to:

- Access your private keys
- Move your funds without your explicit signature
- Approve token spending on your behalf

## Quote and execution integrity

Every quote on 7.Exchange includes built-in safeguards:

- **Unique ID** Each quote has a unique identifier. No duplicates.
- **Single-use** A quote can only be executed once, preventing replay.
- **Time-bound** Quotes expire after a short window. Stale quotes cannot be executed.
- **Session-bound** Quotes are tied to the requesting wallet session.

These properties ensure that the route you approve is the route that executes.

## Provider security

7.Exchange routes through third-party bridge protocols, DEXs, and liquidity sources. Each provider has its own security model, audit history, and risk profile.

7.Exchange evaluates providers before integration but does not control or audit their smart contracts on an ongoing basis. The security of any individual swap depends in part on the security of the provider handling that route.

{% hint style="info" %}
The routing engine's provider-agnostic design means you are not locked into a single provider. If a provider experiences an issue, routes through other providers remain available.
{% endhint %}

## What 7.Exchange does not guarantee

- The security or solvency of third-party bridge protocols
- Immunity from smart contract exploits in underlying providers
- Recovery of funds lost due to bridge failures, exploits, or user error
- Protection against all forms of MEV (though the system mitigates common attack vectors)

## Reporting vulnerabilities

If you discover a security vulnerability, report it through the [Bug Bounty Program](bug-bounty-program.md). Do not disclose vulnerabilities publicly before they are resolved.
