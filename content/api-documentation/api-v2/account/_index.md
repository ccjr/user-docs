---
title: Account
weight: 10
---

# Account API

The accounts API serves important information related to an account,
including account status, funds available for trade, funds available for
withdrawal, and various flags relevant to an account's ability to trade.
An account maybe be blocked for just for trades (`trades_blocked` flag) or for both
trades and transfers (`account_blocked` flag) if Alpaca identifies the account to
engaging in any suspicious activity. Also, in accordance with FINRA's pattern day
trading rule, an account may be flagged for pattern day trading
(`pattern_day_trader flag`), which would inhibit an account from placing any
further day-trades.

{{< rest-endpoint resource="account" method="GET" path="/v2/account" >}}

## Account Entity

### Example
{{< rest-entity-example name="account-v2">}}

### Properties
{{< rest-entity-desc name="account-v2">}}

## Account Status
The following are the possible account status values. Most likely, the
account status is `ACTIVE` unless there is any problem. The account status
may get in `ACCOUNT_UPDATED` when personal information is being updated
from the dashboard, in which case you may not be allowed trading for
a short period of time until the change is approved.

- `ONBOARDING`<br>
  The account is onboarding.
- `SUBMISSION_FAILED`<br>
  The account application submission failed for some reason.
- `SUBMITTED`<br>
  The account application has been submitted for review.
- `ACCOUNT_UPDATED`<br>
  The account information is being updated.
- `APPROVAL_PENDING`<br>
  The final account approval is pending.
- `ACTIVE`<br>
  The account is active for trading.
- `REJECTED`<br>
  The account application has been rejected.
