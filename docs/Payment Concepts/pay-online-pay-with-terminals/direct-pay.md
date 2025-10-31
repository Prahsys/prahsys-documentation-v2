---
title: Pay API
excerpt: >-
  Prahsys provides several convenient ways to gather and manage customer credit
  card details securely. However, you may prefer to manage this information
  directly and provide payment details with each transaction request. Using Pay
  API is incredibly straightforward and gives you complete control over your
  application's payment flow.
deprecated: false
hidden: false
metadata:
  title: Pay API | Prahsys Documentation
  description: >-
    Learn about using Prahsys Payment transactions with direct pay. Complete
    payments by providing card details directly to the API endpoints.
  keywords:
    - direct pay
    - direct card
    - card details
    - card information
    - source of funds
    - payment transactions
    - authorize payments
    - capture payments
    - payment processing
    - verify payments
    - payment APIRetry
  robots: index
---
## What is Pay API?

Pay API allows you to include card details directly in payment API requests, bypassing the need for pay sessions or tokens. While we recommend using Prahsys sessions and tokenization to avoid handling sensitive card information on your servers, Pay API gives you complete control over the payment flow when your application requires it. All you need to do
is pass the card details directly in the request body under the `payment.billing.card` field. Doing so let's you process
a payment in a single API call.

<Callout icon="❗️" theme="error">
  Using Pay API means customer card information passes through your servers, which increases your
  [PCI Compliants](doc:pci-compliants) requirements and security responsibilities.
</Callout>

### Alternatives

Prahsys offers several ways to process payments without you ever needing to touch sensitive customer information. If you still want the control and flexibility that comes with using the API transactions but without collecting card details directly, then consider using [Pay Session](doc:pay-session). Prahsys can collect the sensitive data via iframes in your front-end and store it securely. Then when you're ready to make an API request via your back-end, you can just reference the session id without providing any card info directly. For repeat transactions, you can have Prahsys tokenize the card details which were used in a session.
We'll store the sensitive data and you only need to save the token (learn more about [Tokenization](doc:tokenization)). If you want a turn-key solution requiring the least amount of effort to implement, check out our [Pay Portal](doc:pay-portal).

### Billing

You can provide card info to the Prahsys API by including it under the `"card"` attribute of a `"billing"` object.
For all requests besides tokenization, billing info is provided under the `"payment"` field of the request body.

```json
payment: {
  billing: {
    card: {
      number: "4111111111111111",
      expiry: {
        month: "12",
        year: "25",
      },
      securityCode: "123",
    },
  },
}
```

> Use [Test Payment Cards](doc:test-payment-cards) for testing Pay API operations when testing in the SANDBOX environment.
