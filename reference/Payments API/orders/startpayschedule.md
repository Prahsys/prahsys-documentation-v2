---
title: Start Pay Schedule
excerpt: >-
  Activates the pay schedule for an order. The order must already have pay
  schedule data configured

  (via the Update or Create Order endpoint) but not yet started.


  **For non-autopay schedules:** Creates the first pay period and sends an
  invoice to the customer

  via their configured notification preferences (email/SMS).


  **For autopay schedules:** Processes the first payment immediately using the
  stored or provided payment method,

  then creates the next pay period. A `session` can be provided to tokenize a
  new payment method.
api:
  file: payments-openapi.json
  operationId: startPaySchedule
hidden: false
---