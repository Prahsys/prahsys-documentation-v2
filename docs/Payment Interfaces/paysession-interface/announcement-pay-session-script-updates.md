---
title: 'Announcement: Pay Session Script Updates!'
excerpt: >-
  We've updated the Pay Session script with a new URL structure and added new
  state-based styling methods.
deprecated: false
hidden: true
icon: 📣
metadata:
  robots: index
---
### 1. Update Your Script URL

The script endpoint has moved from `secure.prahsys.com` to `gateway.prahsys.com` with a new URL structure that includes your session ID.

**Old URL:**

```html
<script src="https://secure.prahsys.com/form/version/100/merchant/YOUR_MERCHANT_ID/session.js"></script>
```

**New URL:**

```html
<script src="https://gateway.prahsys.com/n1/merchant/YOUR_MERCHANT_ID/session/YOUR_SESSION_ID/script.js"></script>
```

### 2. Create Session Before Loading the Script

Because the session ID is now part of the script URL, you must call the **Create Session** API before inserting the script tag. The script can no longer be loaded statically (e.g., via a `<script>` tag in your HTML template or a framework component like Next.js `<Script>`). Instead, insert it dynamically after session creation:

```javascript
// 1. Create session via your server
const sessionId = await createSession();

// 2. Dynamically load the script with the session ID
const script = document.createElement("script");
script.src = `https://gateway.prahsys.com/n1/merchant/${merchantId}/session/${sessionId}/script.js`;
script.async = true;
script.onload = () => {
  // 3. Configure the payment session
  window.PaymentSession.configure({ /* ... */ });
};
document.body.appendChild(script);
```

### 3. Use Your Raw Merchant ID

**The `TEST` prefix is no longer required in the script URL or API calls. Use your raw merchant ID directly.**

**Old:**

```
TEST[MERCHANTID]
```

**New:**

```
[MERCHANTID]
```

### 4. Process Payments Inside the `formSessionUpdate` Callback

Previously, a common pattern was to call `updateSessionFromForm("card")` and then use a `setTimeout` to process the payment:

```javascript
// OLD — race-condition prone, do not use
PaymentSession.updateSessionFromForm("card");
setTimeout(() => processPayment(), 1000);
```

The correct approach is to trigger your payment request **inside the `formSessionUpdate` callback** when `status === "ok"`. This guarantees the session has been updated with card data before you attempt to charge:

```javascript
PaymentSession.configure({
  // ...
  callbacks: {
    formSessionUpdate: (response) => {
      if (response.status === "ok") {
        // Session is updated — safe to process payment now
        processPayment();
      } else if (response.status === "fields_in_error") {
        // Handle validation errors
      }
    }
  }
});

// When user clicks "Pay":
PaymentSession.updateSessionFromForm("card");
// Payment will be triggered by the callback above
```

<br />

```json
{
  "payment": {
    "id": "your_payment_id",
    "amount": 50,
    "currency": "USD",
    "description": "Order #1234"
  }
}
```

### New Styling Methods

Added state-based styling methods, callable the same way as existing methods (`setHoverStyle`, `setFocusStyle`, `setPlaceholderStyle`):

* `setErrorStyle()` — Style fields with validation errors (auto-applied on failure)
* `setValidStyle()` — Style fields with valid input
* `setFieldState()` — Programmatically set field states

```javascript
PaymentSession.setErrorStyle(["card.number", "card.securityCode"], {
  borderColor: "#ef4444",
});

PaymentSession.setValidStyle(["card.number"], {
  borderColor: "#10b981",
});
```

## Migration Checklist

* [ ] Update script URL from `secure.prahsys.com` to `gateway.prahsys.com`
* [ ] Create session before loading the script (dynamic script insertion)
* [ ] Remove `TEST` prefix from merchant ID
* [ ] Move payment processing into `formSessionUpdate` callback (remove `setTimeout` pattern)
* [ ] _(Optional)_ Remove `session` parameter from `configure()`
* [ ] _(Optional)_ Add `description` field to session creation
* [ ] _(Optional)_ Add new styling methods (`setErrorStyle`, `setValidStyle`, `setFieldState`)
* [ ] Test in development
* [ ] Deploy to production

## Documentation

For full implementation details, visit the [PaySession interface documentation](https://docs.prahsys.com/docs/paysession-interface).

<br />

<br />
