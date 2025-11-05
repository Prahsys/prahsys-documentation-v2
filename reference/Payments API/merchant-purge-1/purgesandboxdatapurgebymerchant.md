---
title: Purge Sandbox Data
excerpt: >-
  Delete **sandbox** data for the specified merchant. This endpoint allows
  deletion of different entity types related to merchants.

  The operation is performed asynchronously and will trigger the
  "payments.merchant.purge" webhook upon completion.
api:
  file: gateway-openapi.json
  operationId: purgeSandboxDataPurgeByMerchant
hidden: false
---