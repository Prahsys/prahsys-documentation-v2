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

<Callout icon="❗️">
  Using Direct Pay means customer card information passes through your servers, which increases your
  [PCI DSS compliance](\{routes.payments\(\).resources\(\).pciCompliance\(\)}) requirements
  and security responsibilities.
</Callout>

### Alternatives

Prahsys offers several ways to process payments without you ever needing to touch sensitive customer information. If you
still want the control and flexibility that comes with using the API transactions but without collecting card details
directly, then consider using [Pay Session](\{routes.payments\(\).conceptsGuides\(\).paySession\(\)}). Prahsys can collect the sensitive data via iframes in your front-end and store it securely. Then
when you're ready to make an API request via your back-end, you can just reference the session id without providing any
card info directly. For repeat transactions, you can have Prahsys tokenize the card details which were used in a session.
We'll store the sensitive data and you only need to save the token (learn more about tokenization
[here](\{routes.payments\(\).conceptsGuides\(\).tokenization\(\)})). If you want a turn-key solution requiring the least amount of effort to implement,
check out our [Pay Portal Sessions](\{routes.payments\(\).conceptsGuides\(\).payPortal\(\)}).

## Using Direct Pay

Direct Pay can be used with the
[Authorize](\{routes.payments\(\).api\("transactions/authorize"\)}),
[Capture](\{routes.payments\(\).api\("transactions/capture"\)}),
[Pay](\{routes.payments\(\).api\("transactions/pay"\)}), and
[Verify](\{routes.payments\(\).api\("transactions/verify"\)})
transactions (learn more about these transactions
[here](\{routes.payments\(\).conceptsGuides\(\).transactions\(\)})). Card details are always
used within the context of a payment's billing information. You can provide this in the body of a transaction request
or add it to a payment for later use. Billing information can be added to a payment directly via our
[Update or Create Payment](\{routes.payments\(\).api\("payments/updateorcreatepayment"\)}) endpoint,
or in the context of a session through our [Update Session](\{routes.payments\(\).api\("sessions/updatesession"\)}) endpoint.

You can also tokenize card details yourself by providing them directly to the tokenization api (instead of referencing a pay session).
Tokenization is the only operation which will use card details outside the context of a specific payment
([see example in the tokenization guide](\{routes.payments\("concepts-guides/tokenization#pay-api-tokenization-\(higher-pci-scope\)"\)})).

### Billing

You can provide card info to the Prahsys API by including it under the `"card"` attribute of a `"billing"` object.
For all requests besides tokenization, billing info is provided under the `"payment"` field of the request body.

```
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

> **NOTE:** Use [test cards](\{routes.payments\(\).resources\(\).testCards\(\)}) for testing Direct Pay operations when testing in the SANDBOX environment.
