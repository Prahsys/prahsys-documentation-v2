---
title: List Payments
excerpt: >-
  NOTE: This method serves two different routes:
    - GET /organization/{organizationId}/payments
    - GET /merchant/{merchantId}/payments

  We don't add #[UrlParam] attributes here because:

  1. Each route has a different path parameter (organizationId vs merchantId)

  2. Adding both would cause OpenAPI validation errors ("parameter not in path")

  3. Scribe auto-detects path parameters from the route URI, so they're
  documented correctly


  The custom OpenAPISpecWriter::operationId() method handles generating unique
  operation IDs

  for these routes (listPaymentsByOrganization vs listPaymentsByMerchant).
api:
  file: payments-openapi.json
  operationId: listPaymentsByMerchant
hidden: false
---