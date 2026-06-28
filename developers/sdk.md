---
description: TypeScript integration pattern for the live 7.Exchange REST API.
---

# SDK

{% hint style="info" %}
There is no separate public npm SDK required for current integrations. Use the live REST API directly from TypeScript with `fetch`, `axios`, or your preferred HTTP client.
{% endhint %}

## Recommended TypeScript wrapper

Create a small API client in your app so quote, lock, execute, and status calls share the same base URL and error handling.

```ts
type SevenApiOptions = {
  baseUrl?: string;
  token?: string;
  apiKey?: string;
};

export class SevenApi {
  private readonly baseUrl: string;
  private readonly token?: string;
  private readonly apiKey?: string;

  constructor(options: SevenApiOptions = {}) {
    this.baseUrl = options.baseUrl ?? "https://api.7.exchange";
    this.token = options.token;
    this.apiKey = options.apiKey;
  }

  async request<T>(path: string, init: RequestInit = {}): Promise<T> {
    const headers = new Headers(init.headers);
    headers.set("Content-Type", "application/json");

    if (this.token) headers.set("Authorization", `Bearer ${this.token}`);
    if (this.apiKey) headers.set("x-api-key", this.apiKey);

    const response = await fetch(`${this.baseUrl}${path}`, {
      ...init,
      headers,
    });

    const payload = await response.json().catch(() => null);

    if (!response.ok) {
      const message =
        payload && typeof payload === "object" && "error" in payload
          ? String(payload.error)
          : `7.Exchange API request failed with ${response.status}`;
      throw new Error(message);
    }

    return payload as T;
  }
}
```

## Core helpers

```ts
type QuoteRequest = {
  srcChain: string;
  srcAddress: string;
  dstChain: string;
  dstAddress: string;
  amount: string;
  slippage?: number;
  depositor?: string;
  recipient?: string;
  provider?: string;
  quoteId?: string;
  referralCode?: string;
  exclude?: string[];
  namespace?: string;
};

export async function getQuotes(api: SevenApi, input: QuoteRequest) {
  return api.request<unknown[]>("/api/quote", {
    method: "POST",
    body: JSON.stringify(input),
  });
}

export async function lockQuote(api: SevenApi, input: QuoteRequest) {
  return api.request<Record<string, unknown>>("/api/quote/lock", {
    method: "POST",
    body: JSON.stringify(input),
  });
}

export async function executeQuote(api: SevenApi, input: QuoteRequest) {
  return api.request<Record<string, unknown>>("/api/quote/execute", {
    method: "POST",
    body: JSON.stringify(input),
  });
}

export async function getTransactionStatus(
  api: SevenApi,
  lookup: { transactionId?: string; hash?: string },
) {
  const params = new URLSearchParams();
  if (lookup.transactionId) params.set("transactionId", lookup.transactionId);
  if (lookup.hash) params.set("hash", lookup.hash);
  return api.request<Record<string, unknown>>(
    `/api/transaction/status?${params}`,
  );
}
```

## Usage

```ts
const api = new SevenApi();

const quotes = await getQuotes(api, {
  srcChain: "ethereum",
  srcAddress: "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
  dstChain: "arbitrum",
  dstAddress: "0xaf88d065e77c8cC2239327C5EDb3A432268e5831",
  amount: "50",
  slippage: 100,
  depositor: "0xYourSourceWallet",
  recipient: "0xYourDestinationWallet",
});

const selected = quotes[0] as { provider?: string };

const locked = await lockQuote(api, {
  srcChain: "ethereum",
  srcAddress: "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
  dstChain: "arbitrum",
  dstAddress: "0xaf88d065e77c8cC2239327C5EDb3A432268e5831",
  amount: "50",
  slippage: 100,
  provider: selected.provider,
  depositor: "0xYourSourceWallet",
  recipient: "0xYourDestinationWallet",
});

const executed = await executeQuote(api, {
  srcChain: "ethereum",
  srcAddress: "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
  dstChain: "arbitrum",
  dstAddress: "0xaf88d065e77c8cC2239327C5EDb3A432268e5831",
  amount: "50",
  slippage: 100,
  provider: selected.provider,
  quoteId: String(locked.quoteId),
  depositor: "0xYourSourceWallet",
  recipient: "0xYourDestinationWallet",
});
```

## Client generation

If you prefer generated clients, point your OpenAPI generator at:

```text
https://api.7.exchange/api/docs.json
```

Review generated types before relying on them for provider-specific quote objects. Some provider response fields are intentionally dynamic.
