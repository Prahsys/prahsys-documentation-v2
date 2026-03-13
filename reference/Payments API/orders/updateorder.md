---
title: Update Order
excerpt: >-
  Partially update an existing order. Accepts a subset of order fields
  (description, amount, currency, dueDate, customers, paySchedule).

  The order must already exist; returns 404 if not found.
api:
  file: payments-openapi.json
  operationId: updateOrder
hidden: false
---