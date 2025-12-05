---
title: Payouts
excerpt: >-
  Payouts represent the actual transfer of funds from processed transactions to
  your merchant account. When your customers make payments through Prahsys,
  those funds don't arrive instantly—they follow a settlement and payout process
  managed by card networks and payment processors.
deprecated: false
hidden: false
icon: far fa-key-skeleton
metadata:
  robots: index
---
## Overview

This guide explains how payouts work, when you can expect funds, and how the process differs between sandbox and live environments.

## Understanding Settlement States

Before funds reach your account, transactions move through distinct settlement states:

| Payout State | Description                                                                                                                                                            |
| :----------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `pending`    | The transaction has been authorized but settlement details aren't available yet. Funds are awaiting processing.                                                        |
| `settling`   | The transaction has settled on the card network and funds are in the FBO (Funding Bank Organization) account, but haven't been deposited to your merchant account yet. |
| `settled`    | The transaction has been fully processed and funds have been deposited to your merchant account.                                                                       |
| Needs Review | Needs Review                                                                                                                                                           |

## Payout Grouping

Prahsys groups multiple transactions into single payouts based on deposit batches from your payment processor. Rather than receiving individual transfers for each transaction, you'll receive consolidated payouts containing all transactions that settled during the same processing window.

Each payout includes:

* Total settlement amount (sum of all transaction amounts)
* Net amount (settlement amount minus fees)
* Fee breakdown (interchange, network, processor, and assessment fees)
* Individual transaction details for reconciliation

## Sandbox Environment

**Simulated Settlements:**
Sandbox transactions use simulated settlement data that mirrors production behavior without moving real money. This lets you test your integration and understand the payout flow before going live.

**Transaction Grouping:**
Sandbox transactions created within the same 5-minute interval are automatically grouped into the same test payout. For example, transactions processed at 2:31 PM, 2:33 PM, and 2:34 PM will all be grouped together and rounded to 2:35 PM for the payout.

**Instant Processing:**
Unlike live transactions, sandbox settlements are simulated instantly. You'll see transactions move through pending → settling → settled states immediately during testing, allowing you to verify your integration without waiting for actual banking timelines.

<Callout icon="🚧">
  Sandbox transactions don't involve real money or actual bank transfers. They exist solely for testing your integration.
</Callout>

## Live Environment

**Real Settlement Timing:**
Live transactions follow actual card network and banking schedules. After a transaction is authorized, settlement typically takes 2-3 business days depending on the card network and your processor.

**Payout Schedule:**
Payouts occur Monday through Friday on a next-business-day schedule:

* Transactions processed Monday are paid out by end-of-day Tuesday
* Transactions processed Tuesday are paid out by end-of-day Wednesday
* Transactions processed Friday are paid out by end-of-day Monday

The payout schedule follows banking days only—weekends and holidays don't count as business days.

## Payout Status

Payouts move through different statuses based on their transaction states:

| Payout State | Description                                                                                                                                                            |
| :----------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `pending`    | The transaction has been authorized but settlement details aren't available yet. Funds are awaiting processing.                                                        |
| `settling`   | The transaction has settled on the card network and funds are in the FBO (Funding Bank Organization) account, but haven't been deposited to your merchant account yet. |
| `settled`    | The transaction has been fully processed and funds have been deposited to your merchant account.                                                                       |
| Needs Review | Needs Review                                                                                                                                                           |

## Retrieving Payouts

Access your payout information through the Prahsys API:

```bash
curl -X GET "https://api.prahsys.com/merchants/{merchantId}/payouts" \
  -H "Authorization: Bearer $PRAHSYS_API_KEY"
```

The API supports pagination, filtering by date ranges and amounts, and sorting by various fields. Use your sandbox API key (`sk_test_`) to retrieve test payouts, or your live key (`sk_live_`) for production payouts.

## Key Differences: Sandbox vs Live

| Aspect               | Sandbox                       | Live                        |
| -------------------- | ----------------------------- | --------------------------- |
| Settlement Timing    | Instant simulation            | 2-3 business days           |
| Payout Schedule      | Grouped by 5-minute intervals | Next business day (Mon-Fri) |
| Transaction Grouping | Time-based intervals          | Processor deposit batches   |
| Money Movement       | No real funds                 | Actual bank transfers       |
| Purpose              | Testing integration           | Production use              |
