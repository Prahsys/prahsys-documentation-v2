---
title: Webhooks
excerpt: Event reference for webhooks emitted by the payments gateway
deprecated: false
hidden: false
metadata:
  robots: index
---
For everything _around_ webhooks — registering an endpoint, grabbing the signing secret, retry behavior, replaying failed deliveries — see the generic [Webhooks](/docs/webhooks) page. This page is only the catalogue: what events fire, when they fire, and what's in the payload.

## The envelope

Every gateway webhook has the same outer shape:

```json
{
  "eventType": "payments.payment.transaction.pay",
  "payload": { "data": { /* event-specific body */ } },
  "eventId": "<stable idempotency key>"
}
```

A few events add sibling fields next to `data` (for example `previousStatus` and `newStatus` on `orders.status_changed`). Those are called out per-event below.

`eventId` is stable per logical event, so the same event will arrive with the same `eventId` on retries. Dedupe on it.

## Events at a glance

| Event                                                                            | Fires when                                   | Payload type          |
| -------------------------------------------------------------------------------- | -------------------------------------------- | --------------------- |
| [`payments.payment.transaction.authorize`](#paymentspaymenttransactionauthorize) | An authorize-only transaction succeeds       | Transaction           |
| [`payments.payment.transaction.capture`](#paymentspaymenttransactioncapture)     | A prior authorization is captured            | Transaction           |
| [`payments.payment.transaction.pay`](#paymentspaymenttransactionpay)             | A one-shot auth+capture succeeds             | Transaction           |
| [`payments.payment.transaction.refund`](#paymentspaymenttransactionrefund)       | A refund is processed                        | Transaction           |
| [`payments.payment.transaction.void`](#paymentspaymenttransactionvoid)           | An unsettled authorization is voided         | Transaction           |
| [`payments.payment.transaction.verify`](#paymentspaymenttransactionverify)       | A $0 verify / card-on-file check succeeds    | Transaction           |
| [`orders.status_changed`](#ordersstatus_changed)                                 | An order moves between statuses              | Order + status fields |
| [`orders.pay_schedule.started`](#orderspay_schedulestarted)                      | A pay schedule begins                        | Order                 |
| [`orders.pay_schedule.cancelled`](#orderspay_schedulecancelled)                  | A pay schedule is cancelled                  | Order                 |
| [`orders.pay_schedule.period.fulfilled`](#orderspay_scheduleperiodfulfilled)     | A billing period is paid in full             | Order + period fields |
| [`orders.pay_schedule.autopay.failed`](#orderspay_scheduleautopayfailed)         | An autopay attempt fails                     | Order                 |
| [`exports.export.completed`](#exportsexportcompleted)                            | An export finishes and is ready to download  | Export                |
| [`payments.merchant.purge`](#paymentsmerchantpurge)                              | A sandbox merchant data purge finishes       | Purge results         |
| [`test`](#the-test-event)                                                        | You click "Send test event" in the dashboard | Empty                 |

## Payment events

The six transaction events fire on a successful response from the matching transaction endpoint, no matter how the transaction was initiated — direct API call, hosted pay session, terminal, or scheduled autopay. The payload is a full transaction record (the same shape returned by `GET /n1/merchant/{merchantId}/transaction/{transactionId}`).

For these events, `payload` wraps the transaction in the standard API response envelope:

```json
{
  "eventType": "payments.payment.transaction.pay",
  "payload": {
    "success": true,
    "statusCode": 200,
    "data": { /* transaction */ }
  },
  "eventId": "<idempotency key>"
}
```

### `payments.payment.transaction.authorize`

Fires after a successful authorize-only transaction (no funds captured yet). Pair it with `payments.payment.transaction.capture` to track the full auth→capture lifecycle.

```json
{
  "eventType": "payments.payment.transaction.authorize",
  "payload": {
    "success": true,
    "statusCode": 201,
    "data": {
      "id": "019df4e5-1090-71c8-b252-d76657a8b4ad",
      "operation": "AUTHORIZE",
      "type": "AUTHORIZATION",
      "result": "SUCCESS",
      "amount": 49.99,
      "currency": "USD",
      "requestedAmount": 49.99,
      "refundedAmount": 0,
      "refundableAmount": 49.99,
      "settlementStatus": "PENDING",
      "authorizationCode": "AB12CD",
      "avsCode": "FULL_MATCH",
      "cscCode": "MATCH",
      "reference": "ref_ab12cd34",
      "source": "INTERNET",
      "payment": { /* PaymentData */ },
      "createdAt": "2026-05-14T16:42:11+00:00"
    }
  },
  "eventId": "payments.payment.transaction.authorize-019df4e5-1090-71c8-b252-d76657a8b4ad-1747241000"
}
```

### `payments.payment.transaction.capture`

Fires after a capture against a prior authorization. The `targetTransactionId` points at the original auth.

```json
{
  "eventType": "payments.payment.transaction.capture",
  "payload": {
    "success": true,
    "statusCode": 200,
    "data": {
      "id": "019df4e6-2a4b-71c8-9c33-841cc5b921ef",
      "targetTransactionId": "019df4e5-1090-71c8-b252-d76657a8b4ad",
      "operation": "CAPTURE",
      "type": "CAPTURE",
      "result": "SUCCESS",
      "amount": 49.99,
      "currency": "USD",
      "settlementStatus": "SETTLED",
      "settlement": {
        "settlementDate": "2026-05-16T00:00:00+00:00",
        "settlementAmount": 49.99,
        "netAmount": 48.54,
        "totalFees": 1.45,
        "fundingStatus": "FUNDED"
      },
      "payment": { /* PaymentData */ }
    }
  },
  "eventId": "..."
}
```

### `payments.payment.transaction.pay`

The most common event for a typical sale: one-shot authorize + capture. Payload mirrors `capture` — the difference is `operation: "PAY"` and no `targetTransactionId`.

### `payments.payment.transaction.refund`

Fires after a refund clears. `targetTransactionId` points at the original sale or capture. `refundedAmount` and `refundableAmount` reflect cumulative state across all refunds against the parent transaction.

```json
{
  "eventType": "payments.payment.transaction.refund",
  "payload": {
    "success": true,
    "statusCode": 200,
    "data": {
      "id": "019df518-7c1d-7104-a5d2-c4be0a48c1aa",
      "targetTransactionId": "019df4e6-2a4b-71c8-9c33-841cc5b921ef",
      "operation": "REFUND",
      "type": "REFUND",
      "result": "SUCCESS",
      "amount": 49.99,
      "currency": "USD",
      "refundedAmount": 49.99,
      "refundableAmount": 0,
      "payment": { /* PaymentData */ }
    }
  },
  "eventId": "..."
}
```

### `payments.payment.transaction.void`

Fires after an unsettled authorization is voided. After this, `refundableAmount` on the original is 0 — a void is final.

### `payments.payment.transaction.verify`

Fires after a $0 verification (card-on-file checks). Useful for reacting to card-validity confirmations without a real charge.

## Order events

The order events share an `OrderBody` payload — the same object returned by `GET /n1/merchant/{merchantId}/order/{orderId}`. Two of them add extra sibling fields next to `data`; those are called out below.

For these events, `payload` is **not** wrapped in the API response envelope. The shape is:

```json
{
  "eventType": "orders.pay_schedule.started",
  "payload": { "data": { /* order */ } },
  "eventId": "<idempotency key>"
}
```

### `orders.status_changed`

Fires when an order transitions between statuses — for example, `PENDING → PARTIALLY_PAID` after the first payment, or `PARTIALLY_PAID → PAID` once the balance is cleared. `previousStatus` and `newStatus` sit next to `data` on the payload (not inside the order).

```json
{
  "eventType": "orders.status_changed",
  "payload": {
    "data": {
      "id": "PLAN-X7M3-NP2W",
      "merchantId": "Z70B874W63DW",
      "description": "Home Renovation - 12-Month Payment Plan",
      "amount": 1200.00,
      "remainingBalance": 1100.00,
      "currency": "USD",
      "type": "PAYMENT_PLAN",
      "status": "PARTIALLY_PAID",
      "paySchedule": { /* ... */ },
      "customers": [ /* ... */ ],
      "payments": [ /* ... */ ]
    },
    "previousStatus": "PENDING",
    "newStatus": "PARTIALLY_PAID"
  },
  "eventId": "orders.status_changed-019df4e6-4521-7ab3-be14-c82190de5f63-PARTIALLY_PAID-1747241200"
}
```

### `orders.pay_schedule.started`

Fires when you start a pay schedule via `POST /n1/merchant/{merchantId}/order/{orderId}/pay-schedule/start`. The order moves to `SUBSCRIPTION_ACTIVE` (subscriptions) or stays in payment-plan flow with `paySchedule.isActive: true`.

```json
{
  "eventType": "orders.pay_schedule.started",
  "payload": {
    "data": {
      "id": "SUB-MONTHLY-001",
      "merchantId": "Z70B874W63DW",
      "description": "Monthly Software License",
      "amount": null,
      "currency": "USD",
      "type": "SUBSCRIPTION",
      "status": "SUBSCRIPTION_ACTIVE",
      "paySchedule": {
        "recurringAmount": 99.99,
        "currency": "USD",
        "frequency": "MONTHLY",
        "isActive": true,
        "autopay": false,
        "startDate": "2026-05-14",
        "currentDueDate": "2026-06-14",
        "nextReminderDate": "2026-06-07"
      },
      "customers": [ /* ... */ ]
    }
  },
  "eventId": "..."
}
```

### `orders.pay_schedule.cancelled`

Fires when a schedule is cancelled. The payload reflects the post-cancellation state: `paySchedule.isActive: false`, order status `SUBSCRIPTION_CANCELLED` for subscriptions, or whatever terminal status applies for payment plans.

### `orders.pay_schedule.period.fulfilled`

Fires when a billing period is paid in full — useful for tracking which periods have closed without polling. `periodStartDate` and `periodEndDate` sit next to `data`.

```json
{
  "eventType": "orders.pay_schedule.period.fulfilled",
  "payload": {
    "data": { /* full order */ },
    "periodStartDate": "2026-04-14",
    "periodEndDate": "2026-05-14"
  },
  "eventId": "orders.pay_schedule.period.fulfilled-period-019df4f0-..."
}
```

### `orders.pay_schedule.autopay.failed`

Fires when an autopay attempt fails (declined card, expired token, etc.). The payload includes the order's current state — inspect the most recent failed payment in `payments[]` for the decline reason.

## Account & data events

### `exports.export.completed`

Fires when an asynchronous export (transactions, payouts, monthly statements) finishes. Use the `downloadUrl` to pull the file before `downloadUrlExpiresAt`.

```json
{
  "eventType": "exports.export.completed",
  "payload": {
    "data": {
      "id": "export-uuid-456",
      "organizationId": "Z70B874W63DW",
      "organizationType": "MERCHANT",
      "type": "TRANSACTION",
      "format": "XLSX",
      "status": "COMPLETED",
      "rowCount": 1523,
      "fileSize": 245890,
      "downloadUrl": "/api/n1/organization/Z70B874W63DW/exports/export-uuid-456/download?signature=...&expires=...",
      "downloadUrlExpiresAt": "2026-05-15T16:42:11+00:00",
      "completedAt": "2026-05-14T16:42:11+00:00",
      "expiresAt": "2026-05-21T16:42:11+00:00"
    }
  },
  "eventId": "..."
}
```

### `payments.merchant.purge`

**Sandbox only.** Fires after a merchant data purge finishes. The payload reports how many records were removed.

```json
{
  "eventType": "payments.merchant.purge",
  "payload": {
    "data": {
      "merchantId": "Z70B874W63DW",
      "payments": 12,
      "customers": 8,
      "sessions": 5,
      "tokens": 3,
      "exports": 2
    }
  },
  "eventId": "..."
}
```

## The `test` event

When you click **Send test event** for an endpoint in the Prahsys Dashboard, you'll receive an event with `eventType: "test"` and a minimal payload. It's the right way to confirm a fresh endpoint is reachable and that your signature verification is wired up correctly — _before_ a real transaction depends on it going through.

## Handling events well

* **Dedupe on `eventId`.** It's stable across retries.
* **Treat the webhook as a notification, not the source of truth.** If you need fields the payload doesn't include, fetch the transaction or order from the API by ID.
* **Verify the signature on every request.** Use the Svix snippet on the [Webhooks](/docs/webhooks) page — we sign with the standard Svix HMAC-SHA256 scheme.
