---
description: Production swap API integration for 7.Exchange.
---

# Developers

The 7.Exchange production swap API is ready for integrators.

```text
https://api.7.exchange/api/v1
```

The developer docs focus on the public swap integration flow:

- `GET /api/v1/swap/chains`
- `GET /api/v1/swap/assets`
- `GET /api/v1/swap/sources`
- `POST /api/v1/quote`
- `POST /api/v1/quote/lock`
- `POST /api/v1/quote/submit-signature`
- `POST /api/v1/quote/execute`
- `GET /api/v1/transaction/status`
- `POST /api/v1/transaction/track`
- `GET /api/v1/transaction/history`

Read the full [API Reference](api-reference.md) for request fields, examples, and response shapes.

## Affiliate Attribution

Integrators do not generate API keys. Create an account in the 7.Exchange webapp, complete the affiliate flow, create or copy a referral link, and pass the referral link code as `referralCode` in quote lock and execute requests.
