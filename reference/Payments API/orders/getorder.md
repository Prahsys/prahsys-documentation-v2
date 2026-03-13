---
title: Get Order
excerpt: >-
  Retrieve an order by its ID, including associated customers, payments, pay
  schedule, and invoice send history.

  An order is a record linking one or more payments to a service or product.
  Orders can be one of three types:

  - **Unscheduled**: A simple order with no pay schedule (one-time invoice).

  - **Payment Plan**: An order with a pay schedule and a total amount. A fixed
  minimum amount is due each period. Extra payments can be made at any time. The
  plan ends once the total amount is paid in full.

  - **Subscription**: An order with a pay schedule but no total amount. A fixed
  amount is due each period indefinitely until cancelled.
api:
  file: payments-openapi.json
  operationId: getOrder
hidden: false
---