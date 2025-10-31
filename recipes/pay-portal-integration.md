---
title: Pay Portal
description: >-
  Use Pay Portal when you want the fastest integration path with minimal
  frontend development. Simply create a session server-side and redirect
  customers to Prahsys's pre-built, PCI-compliant payment interface (or embed
  it), eliminating the need to build and maintain your own payment form.
hidden: false
recipe:
  color: '#018FF4'
  icon: ✨
---
```javascript Pay Portal
/**
SERVER SIDE
*/
const response = await fetch("https://api.prahsys.com/payments/n1/merchant/{merchantId}/session", {
  method: "POST",
  headers: {
    Authorization: `Bearer ${apiKey}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    "portal": {
        "operation": "PAY",
        "returnUrl": "https://your-store.com/checkout/complete",
        "cancelUrl": "https://your-store.com/checkout/cancel",
        "merchant": {
            "name": "Your Store Name",
            "logo": "https://your-store.com/logo.png"
        }
    },
    "payment": {
				// You can pass in a custom payment id or it can be auto generated for you
        // "id": "my-custom-id-123",
        "amount": 99.99,
        "description": "Premium Product Description"
    }
  }),
});

// Pass session.data.id to your frontend
const session = await response.json

/**
CLIENT SIDE
*/
<script src="https://secure.prahsys.com/static/checkout/checkout.min.js"></script>

/**
CLIENT SIDE
*/
window.Checkout.configure({
  session: {
    id: session.data.id, // The session ID from Step 1
  },
});

/**
CLIENT SIDE
*/
window.Checkout.showPaymentPage();

/**
CLIENT SIDE
*/
<div id="embedded-pay-portal"></div>

window.Checkout.showEmbeddedPage("#embedded-pay-portal");

/**
CLIENT SIDE
*/
// On your return URL handler
const resultIndicator = request.query.resultIndicator;

// Compare with the stored successIndicator
if (resultIndicator === successIndicator) {
  // Payment successful - fulfill the order
} else {
  // Payment failed or was canceled
}
```

```json Response Example
// Creating Session Response
{
    "success": true,
    "message": "Session created",
    "data": {
        "id": "SESSION0002830039903G99356855F1",
        "portal": {
            "successIndicator": "10593f223a41478c"
        }
    }
}
```

# Create Session with Portal

<!-- javascript@1-30 -->

You must add the portal to the body. The merchant properties inside the portal are optional

# Load Checkout Object

<!-- javascript@31-36 -->

You need to load the Checkout Object to the client side window.

# Load Session into Checkout

<!-- javascript@37-44 -->

After generating your session and loading the checkout script, you need to load the session into checkout window.

# (Option 1) Redirect to Pay Portal

<!-- javascript@46-49 -->

You have two options. You can redirect to a prebuilt page for pay portal or you can embed into your website. This is the call to redirect to Pay Portal. 

# (Option 2) Embed Pay Portal

<!-- javascript@51-56 -->

You have two options. You can redirect to a prebuilt page for pay portal or you can embed into your website. This is how you embed it

# Handle Result

<!-- javascript@58-69 -->

After the success performs an action, (such as completes the payment, cancels the payment, timeouts, etc), we will call your returnUrl from the session object created and pass a query parameter resultIndictor. Use the resultIndictor to check the success or failure.