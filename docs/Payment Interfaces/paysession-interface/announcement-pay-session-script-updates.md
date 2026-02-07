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
## Required Changes

### Update Your Script URL

**Old URL:**

```html
<script src="https://secure.prahsys.com/form/version/100/merchant/YOUR_MERCHANT_ID/session.js"></script>
```

**New URL:**

```html
<script src="https://gateway.prahsys.com/n1/merchant/YOUR_MERCHANT_ID/session/YOUR_SESSION_ID/script.js"></script>
```

## Workflow Update

With requirement to include session id in script url, You will need to create session before inserting script tag.

#### Steps:

* Request <Anchor label="Create Session" target="_blank" href="https://docs.prahsys.com/update/reference/createsessionbymerchant">Create Session</Anchor>  to generate session ID
* Insert above script element with corresponding ids
* On script tag load, initialize payment session by calling `window.PaymentSession.configure`
* Rest of the methods functions the same

## Optional Changes

### Simplify Session Configuration

The `session` parameter in `configure()` is now optional since the session ID is embedded in the script URL.

## New Styling Methods

We've added the following new styling methods. They can be called the same way as existing styling methods (`setHoverStyle`, `setFocusStyle`, `setPlaceholderStyle`) with the same parameters:

* `setErrorStyle()` - Style fields with validation errors (automatically applied on validation failure)
* `setValidStyle()` - Style fields with valid input
* `setFieldState()` - Programmatically set field states

**Example:**

```javascript
PaymentSession.setErrorStyle(['card.number', 'card.securityCode'], {
    borderColor: '#ef4444',
    backgroundColor: '#fef2f2'
});

PaymentSession.setValidStyle(['card.number'], {
    borderColor: '#10b981'
});
```

## Migration Checklist

* [ ] Update script URL to new format
* [ ] (Optional) Remove `session` parameter from `configure()`
* [ ] (Optional) Add new styling methods
* [ ] Test in development
* [ ] Deploy to production

## Documentation

For full implementation details, see: [https://docs.prahsys.com/docs/paysession-interface](https://docs.prahsys.com/docs/paysession-interface)

<br />
