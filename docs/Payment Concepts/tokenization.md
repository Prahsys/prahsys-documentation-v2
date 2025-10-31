---
title: Tokenization
excerpt: >-
  Tokenization allows you to securely store customer payment information for
  future use. Rather than storing sensitive payment details, you store a token
  that represents those details, enhancing security and simplifying recurring
  payments.
deprecated: false
hidden: false
metadata:
  title: Tokenization | Prahsys Documentation
  description: >-
    Learn how to implement Prahsys tokenization to securely store customer
    payment information for future use without handling sensitive payment
    details.
  keywords:
    - payment tokenization
    - secure payment storage
    - token management
    - recurring payments
    - PCI compliance
    - payment tokens
    - card tokenization
    - secure vault
    - payment method storage
    - customer payment data
  robots: index
---
TODO: RECEIPE

## How Tokenization Works

1. A customer provides their payment information (through Pay Portal, Pay Session, or Pay API)
2. Prahsys Payments securely captures the payment details
3. The information is tokenized and stored in our secure vault
4. A token is returned to your system to reference the payment method
5. You use this token for future transactions, without handling sensitive payment data

```mermaid
sequenceDiagram
    participant Client
    participant Server
    participant Prahsys
    participant Database

    Server->>Prahsys: Step 1. Create payment session
    Prahsys-->>Server: Step 2. Return session ID
    Server-->>Client: Step 3. Send session ID

    Client->>Client: Step 4. Load payment fields using session ID
    Client->>Server: Step 5. Submit payment with session ID

    Server->>Prahsys: Step 6. Process payment with session ID
    Prahsys-->>Server: Step 7. Payment confirmation

    Server->>Prahsys: Step 8. Request tokenization of payment method
    Prahsys-->>Server: Step 9. Return payment token

    Server->>Database: Step 10. Store payment token for customer
    Database-->>Server: Step 11. Confirm token storage
    Server-->>Client: Step 12. Confirm payment and tokenization
```

## Token Management Operations

The Tokenization API provides several endpoints for creating, retrieving, updating, and deleting tokens.

### Creating a Token

There are multiple ways to create a token, depending on your integration method:

#### Session-Based Tokenization

When using Pay Portal or Pay Session, you can generate tokens from session data:

```javascript filename="Server-side JavaScript - Using a session"
const response = await fetch("https://api.prahsys.com/payments/n1/merchant/{merchantId}/token", {
  method: "POST",
  headers: {
    Authorization: `Bearer ${apiKey}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    session: {
      id: "SESSION0002742481646J67219140J7",
    },
  }),
});
```

> The session must have processed a transaction to tokenize the payment information.

#### Pay API Tokenization (Higher PCI Scope)

When using Pay API, you can tokenize card details directly (requires higher [PCI Compliance](doc:pci-compliants))
Read more about [Pay API](doc:direct-pay)

```javascript filename="Server-side JavaScript - Using card details"
const response = await fetch("https://api.prahsys.com/payments/n1/merchant/{merchantId}/token", {
  method: "POST",
  headers: {
    Authorization: `Bearer ${apiKey}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
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
  }),
});
```

### Retrieving a Token

You can retrieve details about a token:

```javascript filename="Server-side JavaScript"
const response = await fetch(`https://api.prahsys.com/payments/n1/merchant/{merchantId}/token/${tokenId}`, {
  method: "GET",
  headers: {
    Authorization: `Bearer ${apiKey}`,
  },
});
```

### Updating a Token

You can update certain token attributes:

```javascript filename="Server-side JavaScript"
const response = await fetch(`https://api.prahsys.com/payments/n1/merchant/{merchantId}/token/${tokenId}`, {
  method: "PUT",
  headers: {
    Authorization: `Bearer ${apiKey}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    // Update card expiry date
    billing: {
      card: {
        expiry: {
          month: "12",
          year: "26",
        },
      },
    },
  }),
});
```

### Deleting a Token

You can delete a token when it's no longer needed:

```javascript filename="Server-side JavaScript"
const response = await fetch(`https://api.prahsys.com/payments/n1/merchant/{merchantId}/token/${tokenId}`, {
  method: "DELETE",
  headers: {
    Authorization: `Bearer ${apiKey}`,
  },
});
```
