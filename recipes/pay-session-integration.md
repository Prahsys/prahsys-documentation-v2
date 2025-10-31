---
title: Pay Session Integration
description: >-
  This recipe demonstrates the hosted fields implementation for PCI-compliant
  card data collection using Prahsys Pay Sessions. The integration begins
  server-side by creating a payment session via the Payments API
  (/payments/n1/merchant/{merchantId}/session) with payment details (ID, amount,
  description). Your client-side code then loads the session-specific SDK
  (session.js) and configures PaymentSession to bind hosted input fields to your
  HTML form elements (card number, expiry, CVV, cardholder name). These fields
  render as iframes that securely capture sensitive card data without it ever
  touching your application code, maintaining PCI compliance. The SDK includes
  clickjacking mitigation and provides callback handlers for initialized and
  formSessionUpdate events to manage UI state and validation errors. When the
  customer clicks "Pay Now", updateSessionFromForm() securely transmits card
  data to Prahsys, then your code sends the session ID and payment ID to your
  backend. Your server completes the transaction by calling the
  /payment/{paymentId}/pay endpoint with the session ID and amount, which
  processes the payment using the securely stored card data. This architecture
  keeps card data isolated in PCI-compliant hosted fields while giving you full
  control over the payment flow, form styling, and error handling. Note: Sandbox
  testing requires prefixing the merchant ID with "TEST".
hidden: true
recipe:
  color: '#018FF4'
  icon: 🦉
---
```javascript JavaScript
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
    payment: {
      id: "PAYMENT-123",
      amount: 99.99,
      description: "Premium subscription",
    },
  }),
});

// Pass session.data.id to your frontend
const session = await response.json();

/**
CLIENT SIDE
*/
// When processing with a sandbox merchant, you must put the word TEST infront of the merchant ID.
const merchantId = `TEST${merchantId}`
<script src="https://secure.prahsys.com/form/version/100/merchant/{merchantId}/session.js"></script>

/**
CLIENT SIDE
*/
// App
<style id="antiClickjack">
  body {
    display: none !important;
  }
</style>

/**
CLIENT SIDE
*/
<div>Please enter your payment details:</div>
<div>Cardholder Name: <input type="text" id="cardholder-name" class="input-field" value="" readonly /></div>
<div>Card Number: <input type="text" id="card-number" class="input-field" value="" readonly /></div>
<div>Expiry Month: <input type="text" id="expiry-month" class="input-field" value="" readonly /></div>
<div>Expiry Year: <input type="text" id="expiry-year" class="input-field" value="" readonly /></div>
<div>Security Code: <input type="text" id="security-code" class="input-field" value="" readonly /></div>

<button id="payButton" onclick="pay();">Pay Now</button>

/**
CLIENT SIDE
*/
// Configure the Pay Session
window.PaymentSession.configure({
  session: { id: session.data.id },
  fields: {
    // Attach hosted fields to your payment page for a credit card
    card: {
      number: "#card-number",
      securityCode: "#security-code",
      expiryMonth: "#expiry-month",
      expiryYear: "#expiry-year",
      nameOnCard: "#cardholder-name"
    }
  },
  // Specify your clickjacking mitigation option here
  frameEmbeddingMitigation: ["javascript"],
  callbacks: {
    initialized: function(response) {
      // Handle initialization response
      console.log("Session initialized successfully");
    },
    formSessionUpdate: function(response) {
      // Handle form session update response
      console.log("Session updated with card data");
      
      if (response.status) {
        if ("ok" === response.status) {
          console.log("Session updated successfully");
          
          // Card details updated successfully - you can submit the form
          document.getElementById("payButton").disabled = false;
        } else if ("fields_in_error" === response.status) {
          console.log("Session update failed: " + response.errors.join());
          
          // Card details update failed - you can show appropriate error messages
          document.getElementById("payButton").disabled = true;
        } else if ("request_timeout" === response.status) {
          console.log("Session update failed: request timed out");
          
          // Card details update failed - you can show appropriate error messages
          document.getElementById("payButton").disabled = true;
        } else if ("system_error" === response.status) {
          console.log("Session update failed: system error");
          
          // Card details update failed - you can show appropriate error messages
          document.getElementById("payButton").disabled = true;
        }
      }
    }
  }
  
/**
CLIENT SIDE
*/
function pay() {
  // Update the session with the card details
  PaymentSession.updateSessionFromForm('card');
  
  // Here you'd typically send the sessionId to your server to process the payment
  fetch('https://[YOUR-BACKEND]/process-payment', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
			sessionId: session.data.id
			paymentId: session.data.payment.id
    })
  })
    .then(response => response.json())
    .then(data => {
    	if (data.success) {
      	window.location.href = '/success-page';
	    } else {
  	    // Handle payment error
    	  console.error('Payment failed:', data.error);
    	}
  })
    .catch(error => {
    console.error('Error processing payment:', error);
  });
}

/**
SERVER SIDE
*/
async function processPayment(paymentId, sessionId) {
  try {
    const response = await fetch(`https://api.prahsys.com/payments/n1/merchant/{merchantId}/payment/{paymentId}/pay`, {
      method: "POST",
      headers: {
        Authorization: `Bearer ${apiKey}`,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        payment: {
          amount: 99.99,
        },
        session: {
          id: sessionId,
        },
      }),
    });

    const result = await response.json();
    return result;
  } catch (error) {
    console.error("Error processing payment:", error);
    throw error;
  }

```

```json Response Example
// Create Session Object
{
    "success": true,
    "message": "Session created",
    "data": {
        "id": "SESSION0002524762827I68655889L1",
        "updateStatus": "SUCCESS",
        "payment": {
            "id": "my-custom-id-123",
            "amount": 99.99,
            "currency": "USD",
            "description": "Premium Product Description"
        }
    }
}
```

# Create Session

<!-- javascript@ -->

Create the session. You do not have to pass in a payment ID. It will be auto generated if you do not pass a payment ID.

# Load the PaySession Script

<!-- javascript@ -->

When you are loading a SANDBOX merchant, you must pass the word TEST in front of the merchant ID. 

# Apply antiClickJack

<!-- javascript@ -->

Apply click jacking styling and hide the contents of the page. 

# Create Input Fields

<!-- javascript@ -->

Create the input fields that will live on your page. Make sure to make them readonly and apply the exact id that will be passed into the PaySession object.

# Configure PaySession Object

<!-- javascript@ -->

After loading the PaySession script, you need to pass the session ID and the ids of the inputs into the PaySession object. 

# Handle the Payment Submit

<!-- javascript@ -->

When the user presses submit "Pay Now" button, you need  to update the PaySession with the card information from the inputs, then pass the session ID to your backend. 

# Process Payment

<!-- javascript@ -->

Passing the session ID to your backend, perform the VERIFY, AUTHORIZE, CAPTURE or PAY operation.  