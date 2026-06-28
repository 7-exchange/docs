---
description: Affiliate referral setup for swap API integrations.
---

# Partner Integration

Partner attribution uses the same referral-link flow as the 7.Exchange webapp.

1. Create or sign in to a 7.Exchange account.
2. Open the affiliate dashboard in the webapp.
3. Create or copy a referral link.
4. Use the referral code from that link as `referralCode` in `POST /api/quote/lock` and `POST /api/quote/execute`.

The webapp reads referral links from `ref` or `referral` query parameters and sends that value as `referralCode` during swap requests.

There is no public API-key generation flow for integrators.
