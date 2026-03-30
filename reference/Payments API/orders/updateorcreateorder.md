---
title: Update or Create Order
excerpt: >-
  Creates a new order or updates an existing one (upsert). If `orderId` is
  omitted from the URL, one is auto-generated.


  The order type is determined by the data provided:

  - Include a `paySchedule` object **with** an `amount` to create a **Payment
  Plan** (finite, ends when paid in full).

  - Include a `paySchedule` object **without** an `amount` to create a
  **Subscription** (recurring indefinitely until cancelled).

  - Omit `paySchedule` entirely to create an **Unscheduled** order (simple
  one-time invoice).


  When providing pay schedule data for the first time, `paySchedule.frequency`
  and `paySchedule.recurringAmount` are required.

  When updating an existing pay schedule, both fields are optional (existing
  values are preserved).

  The `reminderBeforeDueDays` and `retryAfterDueDays`

  arrays must contain positive integers that do not exceed the frequency's
  maximum offset days

  (daily: 0, weekly: 6, monthly: 27, yearly: 364).
api:
  file: payments-openapi.json
  operationId: updateOrCreateOrder
hidden: false
---