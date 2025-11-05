---
title: Live / Sandbox Merchants
excerpt: Learn the differences between sandbox and live merchants
deprecated: false
hidden: false
icon: far fa-arrows-turn-to-dots
metadata:
  title: Live / Sandbox Merchants | Prahsys Documentation
  description: Learn the differences between sandbox and live merchants
  keywords:
    - Prahsys SANDBOX merchant
    - test API key
    - merchant application
    - Channel Partner testing
    - sandbox integration
    - merchant onboarding
    - payment processing testing
    - Prahsys LIVE merchant
    - merchant application
    - Channel Partner
    - live API key
    - merchant onboarding
    - signing agreement
    - underwriting process
    - webhook integration
    - payment processing
  robots: index
---
# Live vs Sandbox Merchant Accounts

You can create both live and sandbox merchants. Sandbox merchants are used for testing purposes only. You can create a sandbox merchant by using your test API key. This will create a merchant with the type of `SANDBOX` and allow you to test your integration.
LIVE merchant accounts are used for production purposes. You can create a live merchant by using your live API key. This will create a merchant with the type of `LIVE` and allow you to process real transactions.

## Create a `SANDBOX` Merchant

Channel Partners can create new merchants underneath their account. Testing your integration requires testing onboarding merchants. We allow this by allowing you to create sandbox merchants.

1. Submit the merchant application via API or Prahsys Dashboard
2. Start testing

Let's now break down step by step how to create a new merchant.

## Create the Merchant

You will submit the merchant application via an API request. We will return back a body containing the necessary
information for you to start accepting payments when the merchant has been approved.

<Callout theme="default">
  To create SANDBOX merchants, you must use your **test** API Key.
</Callout>

### Via API

This is how the API Request will look. You can get the a sample request body by going to our [postman collection](https://postman.prahsys.com)

```json New Merchant Response
{
  "success": true,
  "message": "Merchant created and application submitted to underwriting team.",
  "data": {
    "merchant": {
      "id": "1234567890",
      "name": "Merchant Name",
      "slug": "merchant-name",
      "type": "SANDBOX",
      "enabledDirectPay": true,
      "readyForProcessing": true,
      "signed": false,
      "applicationId": 1234567890,
      "createdAt": "2023-01-01T00:00:00Z"
    },
    "merchantApplicationId": 1234567890,
    "signingUrl": "https://dashboard.prahsys.com/[merchant_slug]/application"
  }
}
```

Notice a couple of things from the response body.

1. The `type` of the merchant is `SANDBOX`. This means that the merchant is a sandbox merchant and can be used for testing purposes.
2. The `enabledDirectPay` and `readyForProcessing` fields are set to `true`. This means that the merchant is ready to accept payments and can be used for testing purposes.

##### Can you create SANDBOX merchants by using the Prahsys Dashboard?

You cannot create `SANDBOX` merchants via the Prahsys Dashboard. You can only create `SANDBOX` merchants via the API _for now_

## Request Body

Get the full API Specification for the [request body here](https://new-docs.prahsys.com/reference/newmerchant#/) 

<br />