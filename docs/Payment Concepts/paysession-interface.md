---
title: PaySession Interface
excerpt: This is the Prahsys PaySession Interface with all its methods and properties.
deprecated: false
hidden: false
metadata:
  robots: index
---
<Recipe slug="pay-session-integration" title="Pay Session" />

## PaySession Interface

After loading the PaySession object into the window with the PaySession Script:

```html PaySession Script

<script src="https://secure.prahsys.com/form/version/100/merchant/{merchantId}/session.js"></script>
```

<Callout icon="❗️">
  When working with a SANDBOX merchant, you must put the word `TEST` in-front of the merchant id. 

  `let testMerchantId = "TEST${merchantId}"` 
</Callout>
