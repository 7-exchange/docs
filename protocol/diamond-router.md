---
description: On-chain routing, bridging, and swap execution contracts powering 7.Exchange.
---

# Router Architecture Overview

The routing layer is the on chain execution engine responsible for swaps, cross network transfers, and final settlement. It is built using a modular proxy pattern that allows multiple independent components to operate behind a single entry point.

{% hint style="warning" %}
The system is currently under security review and is not yet deployed to production environments. A pre launch security program is active.
{% endhint %}

## Functional Overview

The router acts as a unified interface that forwards incoming requests to specialized internal modules. Each module is responsible for a narrowly scoped responsibility such as asset exchange, cross network transfer, permission control, or emergency handling.

Typical execution flow:

1. An off chain system computes the optimal execution path and prepares transaction data
2. The user authorizes asset access
3. A single transaction is sent to the router
4. The router forwards execution to the appropriate internal module
5. If cross network transfer is involved, a destination handler completes the process on the target network

{% hint style="info" %}
All user interactions are routed through a single entry point regardless of the complexity of the underlying execution path.
{% endhint %}

## Architectural Design

### Modular Proxy Pattern

The system uses a shared storage proxy with pluggable modules.

**Separation of concerns**
Each feature is isolated into a dedicated module, making the system easier to extend and audit.

**Controlled exposure**
Only explicitly registered modules can be executed, reducing unintended attack vectors.

**Single integration point**
External users and integrators interact with one stable address regardless of internal changes.

**Governed upgrades**
All modifications are delayed through a governance mechanism, ensuring transparency before activation.

{% hint style="warning" %}
Only approved modules are exposed for execution. Any unregistered logic is inaccessible, which reduces attack surface but requires strict upgrade procedures.
{% endhint %}

## Core Components

### Entry Layer

* Main proxy responsible for receiving all requests and routing them internally
* Upgrade controller responsible for adding or removing modules
* Introspection layer for querying active modules
* Ownership management layer

### Execution Modules

Modules are grouped by responsibility:

**Transfer modules**
Handle cross network movement of assets, including encoding, validation, and event emission.

**Swap modules**
Execute asset exchanges on a single network with strict validation of allowed interactions.

**Validation layer**
Ensures transaction data conforms to expected formats and allowed operations.

## Security and Control

Administrative capabilities are separated into dedicated modules:

* Access control enforcement at method level
* Emergency shutdown mechanism acting as a circuit breaker
* Allowlist management restricting external interactions
* Recovery mechanism for stuck or misrouted assets

{% hint style="warning" %}
Emergency controls can disable parts of the system in critical scenarios. These controls should only be triggered under strict operational guidelines.
{% endhint %}

## Supporting Components

Additional contracts operate outside the main router but are essential for full execution:

* Destination executor responsible for final settlement
* Network specific receivers handling incoming transfer messages
* Approval helpers enabling signature based permissions
* Token handling utilities for wrapping and transfers

{% hint style="info" %}
Supporting components do not act as entry points. They are invoked as part of the execution pipeline.
{% endhint %}

## Execution Flows

### Single Network Exchange

A user submits a transaction that is validated and executed entirely within one network. Output assets are delivered directly.

{% hint style="info" %}
All interactions are checked against an allowlist before execution to prevent arbitrary external calls.
{% endhint %}

### Cross Network Transfer

A transfer request is validated and initiated. An event is emitted to track progress. Completion occurs on the destination network.

### Combined Exchange and Transfer

A single transaction performs an initial asset conversion followed by a transfer. This reduces friction and ensures atomic execution.

{% hint style="info" %}
Combining operations into a single transaction minimizes user overhead and reduces intermediate risk exposure.
{% endhint %}

### Destination Settlement

Upon arrival on the target network:

1. A receiver processes the incoming message
2. Optional execution steps are performed
3. Final assets are delivered to the recipient

If execution fails, recovery logic is triggered and funds can be reclaimed.

{% hint style="warning" %}
Failure during destination execution does not result in loss of funds. Recovery mechanisms ensure assets remain claimable.
{% endhint %}

## Event System

The system emits structured logs at each stage:

* Transfer initiated
* Exchange completed
* Transfer completed
* Recovery executed

{% hint style="info" %}
Event logs are the primary mechanism for tracking transaction lifecycle across networks.
{% endhint %}

## Upgrade Governance

All system changes are subject to a time delay before execution. This ensures:

* Public visibility of changes
* Time for independent review
* Reduced risk of malicious upgrades

Control is ultimately assigned to a multi party authorization system prior to production deployment.

{% hint style="warning" %}
Upgrades are delayed but not permissionless. Governance authority must be secured and monitored.
{% endhint %}

## Deployment Model

The system operates in a hybrid mode:

**Networks with on chain routing**
Full validation, execution, and tracking occur directly on chain.

**Networks without on chain routing**
Execution is handled off chain, while still enforcing routing integrity at the application level.

{% hint style="info" %}
Hybrid execution ensures broad network coverage while on chain infrastructure is progressively deployed.
{% endhint %}

## Deployment Status

* Development: complete
* Security program: active
* Audit: in progress
* Production deployment: pending