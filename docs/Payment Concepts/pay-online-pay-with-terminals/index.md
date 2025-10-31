---
title: Payments Integrations
excerpt: This guide helps you choose the right payment integration method for payments
deprecated: false
hidden: false
metadata:
  robots: index
---
## Quick Comparison

| Feature                  | Pay Portal      | Pay Session          | Pay API               |
| ------------------------ | --------------- | -------------------- | --------------------- |
| **Code Complexity**      | Minimal         | Moderate             | Advanced              |
| **UI Customization**     | Pre-built only  | Full styling control | Complete control      |
| **Card Data Handling**   | Prahsys-hosted  | Iframe fields        | Your choice           |
| **PCI Compliance Level** | SAQ A (easiest) | SAQ A-EP (moderate)  | SAQ D (most complex)* |
| **Best For**             | Quick launches  | Branded checkout     | Recurring billing     |

***

## Pay Portal: Fastest Implementation

### What It Is

A complete, pre-built payment page hosted by Prahsys. You create a session and redirect customers to our secure checkout.

### How It Works

1. Create a session server-side
2. Add our JavaScript library to your page
3. Redirect or embed the payment form
4. Handle the return callback with success/failure indicator

### Choose Pay Portal When:

* You need to accept payments quickly with minimal development
* You're okay with Prahsys-branded payment UI
* You want the lowest PCI compliance burden
* You're processing one-time payments

<Image align="center" border={false} caption="Pay Portal" src="https://files.readme.io/44f9d7d1f98bb4bf1f660119343d828a170be5eb9ddcacdecbe8aa96365a2187-pay-portal.png" width="600em" />

***

## Pay Session: Branded Checkout Experience

### What It Is

Secure iframe-based payment fields that embed directly into your custom checkout form. You build the form, we handle the card data through iframes.

### How It Works

1. Create a session server-side
2. Build your custom checkout form with HTML/CSS
3. Load our JavaScript library
4. Bind our secure iframe fields to your form inputs
5. Update session with card data when customer submits
6. Process payment server-side

### Choose Pay Session When:

* You want a branded checkout experience matching your PMS
* You need control over form layout and styling
* You want to maintain your UX flow without redirects

```mermaid
flowchart TB
    1[Create Pay Session]
    2[Load JS Library]
    3[Create Payment Fields]
    4[Customer Enters Payment Details]
    5[Customer Submits Payment]
    6[Receive Confirmation]

    1-->2-->3-->4-->5-->6
```

***

## Pay API: Maximum Flexibility & Recurring Billing

### What It Is

Direct API integration giving you complete control. Process payments entirely server-to-server using tokens or direct card data.

### Three Approaches

**1. Session-based (Recommended for one-time payments)**

* Reference a Pay Portal or Pay Session to collect card data
* Process payment server-side using the session ID
* Card data never touches your servers

**2. Token-based (Recommended for recurring payments)**

* Collect card details once via Portal or Session
* Tokenize the card information
* Use tokens for all future server-to-server charges
* Lowest PCI scope for recurring billing

**3. Direct Pay (Highest PCI scope)**

* Pass raw card details directly in API calls
* Your servers handle sensitive card data
* Maximum control but maximum compliance burden

### Transaction Types

* **PAY** - Single-step payment (authorize + capture combined)
* **AUTHORIZE** - Reserve funds without capturing
* **CAPTURE** - Capture previously authorized funds
* **VERIFY** - Validate card details without charging
* **REFUND** - Return funds to customer
* **VOID** - Cancel pending transaction

### Choose Pay API When:

* You're building subscription or recurring billing features
* You need server-to-server payment processing
* You want complete control over the payment flow
* You're building complex payment logic (split payments, installments)
* You need to process payments without user interaction

***

## PCI Compliance Considerations

### Pay Portal (SAQ A - Easiest)

* Card data never touches your servers
* Prahsys handles all sensitive data
* Annual self-assessment questionnaire
* No vulnerability scanning required

### Pay Session (SAQ A-EP - Moderate)

* Card data enters iframes, never your servers
* You control the checkout page HTML/CSS
* Annual self-assessment questionnaire
* Quarterly vulnerability scans required

### Pay API with Direct Pay (SAQ D - Full Scope)

* Card data passes through your servers
* **Highest compliance requirements**
* Annual audit by QSA required
* Quarterly vulnerability scans required
* Extensive security controls needed
