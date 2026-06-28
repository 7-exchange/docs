---
description: Security audit status and reports for 7.Exchange smart contracts.
---

# Audits

7.Exchange requires independent security verification of all smart contracts before production deployment. This page tracks audit progress and will host all published reports.

{% hint style="info" %}
No contracts are deployed to production environments until security review is completed and all critical findings are resolved.
{% endhint %}

## Core routing system

| Detail | Status |
|---|---|
| **Scope** | Main proxy, modular execution components, supporting contracts, and shared logic |
| **Audit status** | In progress |
| **Bug bounty** | Active. See [Bug Bounty Program](../security/bug-bounty-program.md) |
| **Report** | Will be published here upon completion |
| **Deployment** | Pending audit completion |

## Token contract

| Detail | Status |
|---|---|
| **Scope** | Native protocol token |
| **Audit status** | Planned (Q3 2026, prior to token launch) |
| **Report** | Will be published here upon completion |

{% hint style="warning" %}
Token deployment will not occur before a completed audit and public report.
{% endhint %}

## Audit process

All contracts проходят a structured multi stage security pipeline before deployment.

### 1. Internal review

Code is reviewed by the core development team using:

- Automated testing  
- Fuzz testing  
- Static analysis  

Execution flows are simulated end to end across supported transfer and swap routes using forked network environments.

{% hint style="info" %}
Internal testing focuses on correctness, edge cases, and failure handling across full execution paths.
{% endhint %}

### 2. Bug bounty program

Pre deployment contracts are exposed to external researchers through a structured reward program.

- Severity based payouts  
- Clearly defined scope  
- Responsible disclosure requirements  

See [Bug Bounty Program](../security/bug-bounty-program.md) for full details.

{% hint style="warning" %}
Vulnerabilities must not be disclosed publicly before remediation.
{% endhint %}

### 3. Independent audit

External security firms perform a full review of the system.

The review includes:

- Execution correctness  
- Permission and access control design  
- Reentrancy and state consistency  
- Transaction data validation  
- Upgrade safety through delayed governance  
- Allowlist enforcement  
- Failure and recovery handling on destination execution  
- Economic and incentive based attack vectors  

### 4. Report publication

All completed audit reports are published in full on this page.

Each report includes:

- Identified issues  
- Severity classification  
- Resolution details  

{% hint style="info" %}
Transparency is required. All findings, including resolved issues, are disclosed.
{% endhint %}

### 5. Pre deployment hardening

Before deployment, the system undergoes final security configuration:

- Administrative control transferred to multi party authorization  
- Upgrade delay mechanism enabled for all changes  
- Allowed interaction lists fully configured  
- Emergency shutdown procedures tested in simulated environments  
- All contract addresses verified  

{% hint style="warning" %}
Deployment is blocked until all critical and high severity findings are resolved.
{% endhint %}

### 6. Deployment and verification

Contracts are deployed only after successful completion of all prior stages.

- Source code is verified on public explorers  
- Addresses are published in official documentation  
- Systems are monitored during initial rollout  

See [Contract Addresses](contract-addresses.md) for verified deployments.

## Published reports

{% hint style="info" %}
No audit reports have been published yet. Reports will appear here once available.
{% endhint %}

## Responsible disclosure

If a vulnerability is discovered in any contract, whether deployed or under testing, it must be reported through the official program.

See [Bug Bounty Program](../security/bug-bounty-program.md).

{% hint style="danger" %}
Do not disclose vulnerabilities publicly before they are resolved. This can put user funds at risk.
{% endhint %}