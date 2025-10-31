---
title: PayPortal Checkout
excerpt: >-
  After loading the PayPortal script, the Checkout object is loaded to the
  window. Here is the interface for the Checkout.
deprecated: false
hidden: false
metadata:
  robots: index
---
<br />

<Recipe slug="pay-session-integration" title="Pay Session" />

## PaySession Interface

After loading the PaySession object into the window with the PaySession Script:

```html PaySession Script
<script src="https://secure.prahsys.com/form/version/100/merchant/{merchantId}/session.js"></script>
```

<Callout icon="❗️" theme="error">
  When working with a SANDBOX merchant and loading PaySession or PayPortal script, you must put the word `TEST` in-front of the merchant id.
</Callout>
