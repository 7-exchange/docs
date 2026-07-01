---
description: Affiliate referral setup for swap API integrations.
---

# Partner Integration

Partner attribution uses the same referral-link flow as the 7.Exchange webapp.

1. Create or sign in to a 7.Exchange account.
2. Open the affiliate dashboard in the webapp.
3. Create or copy a referral code.
4. Use the referral code as `referralCode` in `POST /api/v1/quote/lock` and `POST /api/v1/quote/execute`.

There is no public API-key generation flow for integrators.
