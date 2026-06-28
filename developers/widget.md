---
description: Build an embedded cross-chain swap experience with the live 7.Exchange REST API.
---

# Widget

{% hint style="info" %}
The current supported integration path is API-based. Build your own embedded swap UI against the REST API, or link users to the hosted 7.Exchange app while using referral attribution.
{% endhint %}

## Current integration options

| Option | Best for | How it works |
|---|---|---|
| **Custom embedded UI** | Wallets, dashboards, and dApps that need full UX control. | Use the REST API to discover assets, fetch quotes, lock a route, execute the payload, and poll status. |
| **Hosted app link** | Partners that want a low-maintenance entry point. | Send users to the 7.Exchange app and include referral attribution where approved. |
| **Server-side routing service** | Apps that do not want browser-origin CORS coordination. | Your backend calls 7.Exchange, signs or prepares provider payloads with your app flow, and returns safe data to your frontend. |

## Embedded UI checklist

To build a widget-like experience today:

1. Cache `GET /api/swap/chains` and `GET /api/swap/assets` for chain and token selectors.
2. Call `POST /api/quote` whenever the user changes source token, destination token, amount, slippage, depositor, or recipient.
3. Display provider routes from the quote response and keep unknown provider fields available for debugging.
4. Lock the selected route with `POST /api/quote/lock`.
5. Use the locked payload to drive wallet signing or direct-send instructions.
6. Execute with `POST /api/quote/execute`.
7. Poll `GET /api/transaction/status` until `final` is `true`.

## Required UX safeguards

- Show the exact source chain, source token, amount, depositor, destination chain, destination token, and recipient before lock and execute.
- Explain that `slippage` is basis points. For example, `100` is 1%.
- For Direct Send routes, show the exact deposit address, memo or tag when present, token, chain, and amount.
- Do not silently retry execution with a different provider. Ask the user to confirm a new route if the quote expires or execution fails.
- Keep API keys on your backend. Browser widgets should only call public endpoints or authenticated user-session endpoints.

{% hint style="warning" %}
Cross-chain transactions are irreversible. A widget integration must make the final send chain, token, amount, and recipient unambiguous before asking the user to sign or manually send funds.
{% endhint %}

## Minimal browser flow

```ts
const quoteResponse = await fetch("https://api.7.exchange/api/quote", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    srcChain: "ethereum",
    srcAddress: "native",
    dstChain: "arbitrum",
    dstAddress: "0xaf88d065e77c8cC2239327C5EDb3A432268e5831",
    amount: "0.05",
    slippage: 100,
    depositor: "0xUserSourceWallet",
    recipient: "0xUserDestinationWallet",
  }),
});

const quotes = await quoteResponse.json();
```

After the user selects a route, pass the selected provider to `POST /api/quote/lock`, then pass the returned `quoteId` to `POST /api/quote/execute`.

## Browser CORS

The backend uses a browser-origin allowlist. If your integration calls the API directly from a browser, coordinate the production origin before launch. Server-to-server integrations do not rely on browser CORS.
