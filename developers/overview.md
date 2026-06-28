---
description: Production swap API integration for 7.Exchange.
---

# Developers

The 7.Exchange production swap API is ready for integrators.

```text
https://api.7.exchange
```

The developer docs focus on the public swap integration flow:

- `GET /api/swap/chains`
- `GET /api/swap/assets`
- `GET /api/swap/sources`
- `POST /api/quote`
- `POST /api/quote/lock`
- `POST /api/quote/execute`
- `GET /api/transaction/history`

Read the full [API Reference](api-reference.md) for request fields, examples, and response shapes.

## Affiliate Attribution

Integrators do not generate API keys. Create an account in the 7.Exchange webapp, complete the affiliate flow, create or copy a referral link, and pass the referral link code as `referralCode` in quote lock and execute requests.
