---
title: Update Payment Customer
excerpt: >-
  Updates the customer associated with a payment. This endpoint can be used to
  update the customer

  even when the payment is locked (has transactions), since the customer data is
  already captured

  in the PaymentState snapshots and updating the Payment's customer reference
  doesn't violate

  transaction immutability.
api:
  file: payments-openapi.json
  operationId: updatePaymentCustomer
hidden: false
---