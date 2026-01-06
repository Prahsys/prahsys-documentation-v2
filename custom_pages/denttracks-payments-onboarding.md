---
title: DentTracks Payments Onboarding
fullscreen: false
hidden: false
---
# Project Plan: DentTracks Payment Processing

#### Project Plan Resources

[Project Management Sheet Link](https://docs.google.com/spreadsheets/d/1oMYVxvg9XYNMrdePIVxN9s_c8PkCrVRPCk3bjEWwdTw/edit?gid=1005540094#gid=1005540094)

## **DentTracks Team**,

This project plan focuses on implementing secure payment processing for your application using Prahsys Pay Session. After completing merchant onboarding, you're ready to accept payments from your customers. This implementation provides PCI-compliant card processing with iframe-based security, ensuring sensitive card data never touches your servers.

The project is structured around **6 key milestones**, each with specific deliverables designed to ensure a smooth payment processing integration. Milestones 1-2 are required for basic payment functionality, while milestones 3-5 are optional enhancements that add terminal support, real-time webhooks, and refund capabilities.

<Cards columns={3}>
  <Card title="1. Setup Backend API" icon="fa-server">
    Create secure server endpoints for session creation and payment processing. Requires your Prahsys API key and 1 day of backend development.
  </Card>

  <Card title="2. Integrate React Components" icon="fa-code">
    Download and integrate pre-built React payment components. Includes PrahsysPaymentProvider and PaymentForm with full customization options.
  </Card>

  <Card title="3. Terminal Support" icon="fa-credit-card">
    Optional: Add physical terminal support for in-person card-present transactions alongside online payments.
  </Card>

  <Card title="4. Payment Webhooks" icon="fa-bell">
    Optional but Recommended: Receive real-time payment status updates (success, failure, refunds) to keep your application synchronized.
  </Card>

  <Card title="5. Refunds & Voids" icon="fa-undo">
    Optional: Implement refund and void functionality to handle payment reversals and cancellations.
  </Card>

  <Card title="6. Testing & Production" icon="fa-check-circle">
    Comprehensive testing across success/failure scenarios, sandbox verification, and production deployment checklist.
  </Card>
</Cards>


***

## Prerequisites

Before starting this project, ensure you have completed:

1. **Merchant Onboarding** - Your practice must be approved as a Prahsys merchant
   * See: [Merchant Onboarding Project Plan](doc:merchant-onboarding-project-plan)
   * Your merchant must have `APPROVED` status
   * You should have your `merchantId` ready

2. **Prahsys Dashboard Access**
   * Access to [Prahsys Dashboard](https://dashboard.prahsys.com/)
   * TEST API key for development
   * LIVE API key for production (when ready)

3. **Development Environment**
   * React 18+ application
   * Node.js backend (Next.js, Express, or similar)
   * Package manager (npm, pnpm, or yarn)

***

## How Payment Processing Works

Prahsys Pay Session uses iframe-based card collection to keep your PCI compliance requirements minimal. Here's the flow:

**Client-Side Flow:**

1. Customer navigates to your payment page
2. Your app requests a payment session from your backend
3. Backend creates a session with Prahsys and returns session ID
4. Client loads secure iframe fields for card entry
5. Customer enters payment details in isolated iframes
6. Customer clicks "Pay Now"
7. Client triggers payment processing through your backend

**Server-Side Flow:**

1. Backend receives payment request with session ID
2. Backend calls Prahsys API to process payment
3. Prahsys securely processes the transaction
4. Backend receives payment result
5. Backend returns result to client
6. Client shows success or error message

**Security Benefits:**

* Card data never touches your server (iframe isolation)
* Reduced PCI compliance scope
* Secure tokenization by Prahsys
* Encrypted transmission

***

## React Component Library

We've built a complete React component library for you to download and integrate. The components are framework-agnostic, unstyled, and fully customizable.

**What's Included:**

* `PrahsysPaymentProvider` - Main provider for session management
* `PaymentForm` - Complete payment form with validation
* Individual section components (card details, billing, customer info, terminals)
* TypeScript types and Zod validation
* Backend integration examples (Next.js, Express)
* Styling examples (CSS, styled-jsx)

**Download Location:**

```
/examples/react/unstyled/PrahsysPaymentComponents/
```

Copy this entire folder into your project to get started.

***

## 1. Setup Backend API

Create secure backend endpoints to interact with the Prahsys Gateway API. These endpoints handle session creation and payment processing while keeping your API key secure on the server.

**Estimated Time**: 1 day

### Tasks

* [ ] **Get your Prahsys API key**
  * Log in to [Prahsys Dashboard](https://dashboard.prahsys.com/)
  * Navigate to Developers > API Keys
  * Copy your TEST API key for development
  * Add to environment variables (never commit to git)

* [ ] **Set up environment variables**
  * Create `.env.local` (or equivalent)
  * Add `PRAHSYS_API_KEY=your_test_api_key_here`
  * Add to `.gitignore` if not already present

* [ ] **Create payment session endpoint**
  * Route: `POST /api/prahsys/create-session`
  * Accepts: `{ merchantId: string, data?: object }`
  * Calls: `PrahsysGateway.session.create()`
  * Returns: `{ sessionId: string }`

* [ ] **Create payment processing endpoint**
  * Route: `POST /api/prahsys/process-payment`
  * Accepts: `{ merchantId: string, data: PayRequest, paymentId?: string }`
  * Calls: `PrahsysGateway.transaction.pay()`
  * Returns: `TransactionResponse`

* [ ] **Test endpoints with API client**
  * Use Postman, Thunder Client, or curl
  * Verify session creation returns valid session ID
  * Verify payment processing returns transaction data
  * Test error handling (invalid merchant, missing fields)

### Understanding Pay Session

Pay Session is Prahsys's iframe-based payment solution that provides secure card data collection without requiring you to handle sensitive information on your servers.

**Key Benefits:**

1. **PCI Compliance** - Card data is isolated in secure iframes, reducing your PCI compliance scope
2. **Your Branding** - Full control over styling and layout while maintaining security
3. **Secure by Design** - Card details never pass through your servers
4. **Modern Integration** - Simple JavaScript API for configuration and payment processing

**How It Works:**

The Pay Session flow involves three main phases:

**Phase 1: Session Creation (Server)**

```javascript
// Your backend creates a session with Prahsys
const session = await PrahsysGateway.session.create({
  merchantId: "MERCHANT123",
  apiKey: process.env.PRAHSYS_API_KEY,
  data: {
    payment: {
      id: "PAYMENT-123",
      amount: 99.99,
      description: "Premium subscription"
    }
  }
});

// Returns: { id: "SESSION_abc123..." }
```

**Phase 2: Field Configuration (Client)**

```javascript
// Client loads the secure iframe fields
window.PaymentSession.configure({
  session: sessionId,
  fields: {
    card: {
      number: "#card-number",
      securityCode: "#security-code",
      expiryMonth: "#expiry-month",
      expiryYear: "#expiry-year",
      nameOnCard: "#cardholder-name"
    }
  },
  callbacks: {
    initialized: (response) => {
      // Fields are ready for input
    },
    formSessionUpdate: (response) => {
      // Card data has been validated and stored in session
    }
  }
});
```

**Phase 3: Payment Processing (Server)**

```javascript
// Your backend processes the payment using the session ID
const result = await PrahsysGateway.transaction.pay({
  merchantId: "MERCHANT123",
  paymentId: "PAYMENT-123",
  apiKey: process.env.PRAHSYS_API_KEY,
  data: {
    payment: {
      amount: 99.99
    },
    session: {
      id: sessionId  // Contains the secure card data
    }
  }
});
```

### Backend Implementation

<Accordion title="Next.js App Router Example" icon="fa-code">
  ```typescript
  // File: app/api/prahsys/create-session/route.ts

  import { NextRequest, NextResponse } from "next/server";
  import { PrahsysGateway } from "@/lib/services/prahsys-gateway/prahsys-gateway";

  export async function POST(request: NextRequest) {
    try {
      const body = await request.json();
      const { merchantId, data } = body;

      // Validate required fields
      if (!merchantId) {
        return NextResponse.json(
          { error: "merchantId is required" },
          { status: 400 }
        );
      }

      // Call Prahsys Gateway to create session
      const result = await PrahsysGateway.session.create({
        merchantId,
        apiKey: process.env.PRAHSYS_API_KEY!,
        data: data || {},
      });

      if (result.isErr()) {
        console.error("Error creating payment session:", result.error);
        return NextResponse.json(
          { error: result.error.message },
          { status: 500 }
        );
      }

      return NextResponse.json({ sessionId: result.value.id });
    } catch (error) {
      console.error("Unexpected error:", error);
      return NextResponse.json(
        { error: "Failed to create payment session" },
        { status: 500 }
      );
    }
  }
  ```

  ```typescript
  // File: app/api/prahsys/process-payment/route.ts

  import { NextRequest, NextResponse } from "next/server";
  import { PrahsysGateway } from "@/lib/services/prahsys-gateway/prahsys-gateway";

  export async function POST(request: NextRequest) {
    try {
      const body = await request.json();
      const { merchantId, data, paymentId } = body;

      // Validate required fields
      if (!merchantId || !data) {
        return NextResponse.json(
          { error: "merchantId and data are required" },
          { status: 400 }
        );
      }

      // Call Prahsys Gateway to process payment
      const result = await PrahsysGateway.transaction.pay({
        merchantId,
        paymentId,
        apiKey: process.env.PRAHSYS_API_KEY!,
        data,
      });

      if (result.isErr()) {
        console.error("Error processing payment:", result.error);
        return NextResponse.json(
          { error: result.error.message },
          { status: 500 }
        );
      }

      return NextResponse.json(result.value);
    } catch (error) {
      console.error("Unexpected error:", error);
      return NextResponse.json(
        { error: "Failed to process payment" },
        { status: 500 }
      );
    }
  }
  ```

  **See the complete example:** `/examples/react/unstyled/backend-examples/next-app-router.ts`
</Accordion>

<Accordion title="Express.js Example" icon="fa-code">
  ```javascript
  // File: routes/prahsys.js

  const express = require('express');
  const router = express.Router();
  const { PrahsysGateway } = require('./lib/prahsys-gateway');

  // Create payment session
  router.post('/create-session', async (req, res) => {
    try {
      const { merchantId, data } = req.body;

      if (!merchantId) {
        return res.status(400).json({ error: 'merchantId is required' });
      }

      const result = await PrahsysGateway.session.create({
        merchantId,
        apiKey: process.env.PRAHSYS_API_KEY,
        data: data || {},
      });

      if (result.isErr()) {
        console.error('Error creating payment session:', result.error);
        return res.status(500).json({ error: result.error.message });
      }

      res.json({ sessionId: result.value.id });
    } catch (error) {
      console.error('Unexpected error:', error);
      res.status(500).json({ error: 'Failed to create payment session' });
    }
  });

  // Process payment
  router.post('/process-payment', async (req, res) => {
    try {
      const { merchantId, data, paymentId } = req.body;

      if (!merchantId || !data) {
        return res.status(400).json({
          error: 'merchantId and data are required'
        });
      }

      const result = await PrahsysGateway.transaction.pay({
        merchantId,
        paymentId,
        apiKey: process.env.PRAHSYS_API_KEY,
        data,
      });

      if (result.isErr()) {
        console.error('Error processing payment:', result.error);
        return res.status(500).json({ error: result.error.message });
      }

      res.json(result.value);
    } catch (error) {
      console.error('Unexpected error:', error);
      res.status(500).json({ error: 'Failed to process payment' });
    }
  });

  module.exports = router;
  ```

  **See the complete example:** `/examples/react/unstyled/backend-examples/express.ts`
</Accordion>

### Error Handling

Implement proper error handling for common scenarios:

| Error Type           | HTTP Status | Example Message                         |
| -------------------- | ----------- | --------------------------------------- |
| Missing merchantId   | 400         | "merchantId is required"                |
| Invalid API key      | 401         | "Unauthorized - Invalid API key"        |
| Merchant not found   | 404         | "Merchant not found"                    |
| Invalid payment data | 400         | "Invalid payment amount"                |
| Payment declined     | 402         | "Payment declined - Insufficient funds" |
| Gateway error        | 500         | "Payment gateway error"                 |
| Network timeout      | 504         | "Request timeout"                       |

<Callout icon="🔒" theme="warning">
  **Security Best Practices**

  * Never expose your API key in client-side code
  * Always validate input on the server
  * Use HTTPS for all API endpoints
  * Implement rate limiting to prevent abuse
  * Log errors securely without exposing sensitive data
  * Never store raw card data on your server
</Callout>

### Testing Your Backend

Use an API client to test your endpoints before integrating with the frontend:

**Test Session Creation:**

```bash
curl -X POST http://localhost:3000/api/prahsys/create-session \
  -H "Content-Type: application/json" \
  -d '{
    "merchantId": "YOUR_MERCHANT_ID",
    "data": {}
  }'

# Expected Response:
# { "sessionId": "SESSION_abc123..." }
```

**Test Payment Processing:**

```bash
curl -X POST http://localhost:3000/api/prahsys/process-payment \
  -H "Content-Type: application/json" \
  -d '{
    "merchantId": "YOUR_MERCHANT_ID",
    "paymentId": "PAYMENT-123",
    "data": {
      "payment": {
        "amount": 10.00,
        "description": "Test payment"
      },
      "session": {
        "id": "SESSION_abc123..."
      }
    }
  }'

# Expected Response:
# { "payment": { "id": "...", "status": "APPROVED" }, ... }
```

***

## 2. Integrate React Components

Download the pre-built React components and integrate them into your application. These components handle all the complexity of payment session management, secure card field rendering, and payment processing.

**Estimated Time**: 2-3 days

### Tasks

* [ ] **Install required dependencies**
  ```bash
  npm install react-hook-form zod @hookform/resolvers
  # or
  pnpm add react-hook-form zod @hookform/resolvers
  # or
  yarn add react-hook-form zod @hookform/resolvers
  ```

* [ ] **Download React components**
  * Copy `/examples/react/unstyled/PrahsysPaymentComponents/` folder
  * Paste into your project (e.g., `src/components/PrahsysPaymentComponents/`)
  * Verify all files are present (provider, components, types, utils)

* [ ] **Set up PrahsysPaymentProvider**
  * Import `PrahsysPaymentProvider` into your app
  * Configure with merchantId and API callbacks
  * Wrap your payment page or app with provider

* [ ] **Implement PaymentForm component**
  * Import `PaymentForm` component
  * Pass required props (merchantId)
  * Configure optional props (callbacks, show/hide sections)

* [ ] **Test payment flow in sandbox**
  * Use TEST merchant ID (prefix with "TEST" if sandbox)
  * Enter test card number: 5123456789012346
  * Verify payment success response
  * Check payment in Prahsys Dashboard

### Component Overview

The React component library is organized into three main parts:

1. **Provider** - `PrahsysPaymentProvider` manages session state and script loading
2. **Form** - `PaymentForm` is the complete payment form with all sections
3. **Sections** - Individual components for each part of the form (optional, for custom layouts)

```
PrahsysPaymentComponents/
├── provider/
│   └── PrahsysPaymentProvider.tsx    # Main provider
├── components/
│   ├── PaymentForm.tsx               # Complete form
│   └── sections/                     # Individual sections
│       ├── PaymentDetailsSection.tsx
│       ├── CustomerDetailsSection.tsx
│       ├── BillingDetailsSection.tsx
│       ├── PaymentMethodSection.tsx
│       ├── CardDetailsSection.tsx
│       └── TerminalSelectorSection.tsx
├── types/
│   ├── index.ts                      # Shared types
│   └── config.ts                     # Configuration types
├── utils/
│   └── validation.ts                 # Zod schemas
└── index.ts                          # Public exports
```

### Basic Integration

<Accordion title="Step 1: Wrap Your App with Provider" icon="fa-1">
  ```tsx
  // app/payment/page.tsx (Next.js example)

  import { PrahsysPaymentProvider, PaymentForm } from '@/components/PrahsysPaymentComponents';

  export default function PaymentPage() {
    return (
      <PrahsysPaymentProvider
        config={{
          // Your merchant ID (use TEST prefix for sandbox)
          merchantId: "YOUR_MERCHANT_ID",

          // Callback to create payment session
          onCreateSession: async (merchantId, data) => {
            const response = await fetch('/api/prahsys/create-session', {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: JSON.stringify({ merchantId, data })
            });

            if (!response.ok) {
              throw new Error('Failed to create session');
            }

            return response.json(); // { sessionId: "..." }
          },

          // Callback to process payment
          onProcessPayment: async (merchantId, data, paymentId) => {
            const response = await fetch('/api/prahsys/process-payment', {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: JSON.stringify({ merchantId, data, paymentId })
            });

            if (!response.ok) {
              throw new Error('Failed to process payment');
            }

            return response.json(); // TransactionResponse
          }
        }}
      >
        <div style={{ padding: '2rem', maxWidth: '40rem', margin: '0 auto' }}>
          <h1>Payment</h1>
          <PaymentForm merchantId="YOUR_MERCHANT_ID" />
        </div>
      </PrahsysPaymentProvider>
    );
  }
  ```

  That's it! This gives you a fully functional payment form with:

  * Payment amount and description fields
  * Optional customer details section
  * Optional billing address section
  * Secure card input fields (iframe-based)
  * Form validation with error messages
  * Loading and processing states
</Accordion>

<Accordion title="Step 2: Add Success and Error Callbacks" icon="fa-2">
  ```tsx
  export default function PaymentPage() {
    const handlePaymentSuccess = (paymentId: string, data: any) => {
      console.log('Payment successful!', paymentId);
      // Redirect to success page
      // Update UI
      // Send confirmation email
      window.location.href = `/payment/success?id=${paymentId}`;
    };

    const handleError = (error: Error, context: any) => {
      console.error(`Error in ${context.component}.${context.action}:`, error);
      // Send to error tracking (Sentry, LogRocket, etc.)
      // Show user-friendly error message
    };

    return (
      <PrahsysPaymentProvider
        config={{
          merchantId: "YOUR_MERCHANT_ID",
          onCreateSession: async (merchantId, data) => { /* ... */ },
          onProcessPayment: async (merchantId, data, paymentId) => { /* ... */ },

          // Optional: Success callback
          onPaymentSuccess: handlePaymentSuccess,

          // Optional: Error callback
          onError: handleError,
        }}
      >
        <PaymentForm
          merchantId="YOUR_MERCHANT_ID"
          onPaymentSuccess={(paymentId) => handlePaymentSuccess(paymentId, {})}
        />
      </PrahsysPaymentProvider>
    );
  }
  ```
</Accordion>

<Accordion title="Step 3: Customize Styling" icon="fa-3">
  The components use `data-prahsys` attributes for styling. You can target these with CSS:

  ```css
  /* style.css */

  /* Section containers */
  [data-prahsys="section"] {
    background: #ffffff;
    border: 1px solid #e5e7eb;
    border-radius: 8px;
    padding: 1.5rem;
    margin-bottom: 1rem;
  }

  /* Section titles */
  [data-prahsys="section-title"] {
    font-size: 1.125rem;
    font-weight: 600;
    color: #111827;
    margin-bottom: 0.5rem;
  }

  /* Input fields */
  [data-prahsys="input"],
  [data-prahsys="card-input"] {
    width: 100%;
    padding: 0.75rem;
    border: 1px solid #d1d5db;
    border-radius: 6px;
    font-size: 1rem;
  }

  [data-prahsys="input"]:focus,
  [data-prahsys="card-input"]:focus {
    outline: none;
    border-color: #3b82f6;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
  }

  /* Error states */
  [data-prahsys="input"][data-error="true"],
  [data-prahsys="card-input"][data-error="true"] {
    border-color: #ef4444;
  }

  [data-prahsys="error-message"] {
    color: #ef4444;
    font-size: 0.875rem;
    margin-top: 0.25rem;
  }

  /* Submit button */
  [data-prahsys="submit-button"] {
    width: 100%;
    padding: 0.75rem 1.5rem;
    background: #3b82f6;
    color: white;
    font-weight: 600;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    transition: background 0.2s;
  }

  [data-prahsys="submit-button"]:hover:not(:disabled) {
    background: #2563eb;
  }

  [data-prahsys="submit-button"]:disabled {
    background: #9ca3af;
    cursor: not-allowed;
  }
  ```

  **See more examples:** `/examples/react/unstyled/styling-examples/`
</Accordion>

### Provider Configuration

The `PrahsysPaymentProvider` accepts a `config` object with the following options:

| Property                   | Type     | Required | Description                            |
| -------------------------- | -------- | -------- | -------------------------------------- |
| `merchantId`               | string   | Yes      | Your Prahsys merchant ID               |
| `onCreateSession`          | Function | Yes      | Callback to create payment session     |
| `onProcessPayment`         | Function | Yes      | Callback to process payment            |
| `onCheckTerminals`         | Function | No       | Callback to check for terminals        |
| `scriptUrl`                | string   | No       | Override Mastercard script URL         |
| `styling.focusColor`       | string   | No       | Focus state color (default: "#0066cc") |
| `styling.errorColor`       | string   | No       | Error state color (default: "#dc2626") |
| `styling.placeholderColor` | string   | No       | Placeholder color (default: "#9ca3af") |
| `onError`                  | Function | No       | Global error handler                   |
| `onPaymentSuccess`         | Function | No       | Success callback                       |

### PaymentForm Props

The `PaymentForm` component accepts the following props:

| Property              | Type     | Required | Default | Description                  |
| --------------------- | -------- | -------- | ------- | ---------------------------- |
| `merchantId`          | string   | Yes      | -       | Your merchant ID             |
| `onCheckTerminals`    | Function | No       | -       | Check for terminals callback |
| `showCustomerDetails` | boolean  | No       | true    | Show customer info section   |
| `showBillingDetails`  | boolean  | No       | true    | Show billing address section |
| `showTerminalOption`  | boolean  | No       | true    | Show terminal payment option |
| `onPaymentSuccess`    | Function | No       | -       | Success callback             |

### Using the Hook

Access payment state from any component within the provider:

```tsx
import { usePrahsysPayment } from '@/components/PrahsysPaymentComponents';

function PaymentStatus() {
  const {
    isScriptLoaded,
    isReady,
    isLoading,
    sessionId,
    error,
    isProcessing,
    fieldErrors,
    paymentMethod,
  } = usePrahsysPayment();

  return (
    <div>
      {isLoading && <p>Initializing payment session...</p>}
      {isProcessing && <p>Processing payment...</p>}
      {error && <p className="error">{error}</p>}
      {fieldErrors.length > 0 && (
        <ul>
          {fieldErrors.map((err, i) => (
            <li key={i}>{err}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

### Sandbox Testing

When testing in sandbox mode, you must use the TEST prefix for your merchant ID:

```tsx
// Production merchant ID
const merchantId = "MERCHANT123";

// Sandbox merchant ID (add TEST prefix)
const sandboxMerchantId = "TESTMERCHANT123";

// The PrahsysPaymentProvider automatically handles this:
<PrahsysPaymentProvider
  config={{
    merchantId: "MERCHANT123",
    isSandboxMerchant: true,  // Automatically adds TEST prefix
    // ...
  }}
>
```

**Test Card Numbers:**

| Card Type             | Number           | CVV          | Expiry          |
| --------------------- | ---------------- | ------------ | --------------- |
| Mastercard (Success)  | 5123456789012346 | Any 3 digits | Any future date |
| Visa (Success)        | 4111111111111111 | Any 3 digits | Any future date |
| Mastercard (Declined) | 5100000000000000 | Any 3 digits | Any future date |

<Callout icon="💡" theme="info">
  **Sandbox Environment Notes**

  * Always use TEST prefix for merchant ID in sandbox
  * Payments are not real - no actual charges occur
  * Use test card numbers only (real cards will be declined)
  * Session and payment IDs are valid but for testing only
  * Terminal payments are auto-approved in sandbox
</Callout>

***

## 3. Add Terminal Support (Optional)

Enable physical payment terminal support for in-person card-present transactions. This is optional and only needed if you plan to accept payments using card readers or terminals.

**Estimated Time**: 1-2 days

### Tasks

* [ ] **Create terminal check endpoint**
  * Route: `GET /api/prahsys/check-terminals`
  * Accepts: `merchantId` query parameter
  * Returns: `{ hasReadyTerminals: boolean, terminals?: Terminal[] }`
  * Implement logic to check terminal availability

* [ ] **Enable terminal option in PaymentForm**
  * Set `showTerminalOption={true}` on PaymentForm (default)
  * Pass `onCheckTerminals` callback to PaymentForm
  * Verify terminal selector appears when available

* [ ] **Test terminal payment flow**
  * Select terminal payment method
  * Choose a terminal from the list
  * Submit payment
  * Verify payment processes through terminal

* [ ] **Handle terminal connectivity issues**
  * Show clear error if no terminals are ready
  * Provide fallback to manual card entry
  * Add retry mechanism for terminal checks

### When to Use Terminal Payments

Terminal payments are ideal for:

* **In-person transactions** - Customer is physically present at your location
* **Card-present scenarios** - Card can be inserted, tapped, or swiped
* **Lower processing fees** - Card-present rates are typically lower
* **Reduced fraud risk** - Physical card verification provides additional security

**Terminal vs Manual Entry:**

| Feature               | Terminal Payment     | Manual Entry              |
| --------------------- | -------------------- | ------------------------- |
| **Customer Location** | In-person            | Remote/Online             |
| **Card Type**         | Physical card        | Card details              |
| **Processing Fee**    | Lower (card-present) | Higher (card-not-present) |
| **Fraud Risk**        | Lower                | Higher                    |
| **Setup Required**    | Physical terminal    | None                      |
| **PCI Scope**         | Minimal              | Moderate                  |

### Terminal Setup Requirements

Before implementing terminal payments, ensure:

1. **Physical Terminal** - You have a Prahsys-approved payment terminal
2. **Terminal Configuration** - Terminal is configured in Prahsys Dashboard
3. **Network Connection** - Terminal has stable internet connection
4. **Terminal Status** - Terminal shows as "READY" status

### Backend Implementation

<Accordion title="Terminal Check Endpoint (Next.js)" icon="fa-code">
  ```typescript
  // File: app/api/prahsys/check-terminals/route.ts

  import { NextRequest, NextResponse } from "next/server";
  import { PrahsysGateway } from "@/lib/services/prahsys-gateway/prahsys-gateway";

  export async function GET(request: NextRequest) {
    try {
      const searchParams = request.nextUrl.searchParams;
      const merchantId = searchParams.get("merchantId");

      if (!merchantId) {
        return NextResponse.json(
          { error: "merchantId is required" },
          { status: 400 }
        );
      }

      // Call Prahsys Gateway to get terminals
      const result = await PrahsysGateway.terminal.list({
        merchantId,
        apiKey: process.env.PRAHSYS_API_KEY!,
      });

      if (result.isErr()) {
        console.error("Error checking terminals:", result.error);
        return NextResponse.json(
          { error: result.error.message },
          { status: 500 }
        );
      }

      const terminals = result.value;
      const readyTerminals = terminals.filter(t => t.status === 'READY');

      return NextResponse.json({
        hasReadyTerminals: readyTerminals.length > 0,
        terminals: readyTerminals,
      });
    } catch (error) {
      console.error("Unexpected error:", error);
      return NextResponse.json(
        { error: "Failed to check terminals" },
        { status: 500 }
      );
    }
  }
  ```
</Accordion>

### React Integration

```tsx
import { PrahsysPaymentProvider, PaymentForm } from '@/components/PrahsysPaymentComponents';

export default function PaymentPage() {
  // Terminal check callback
  const checkTerminals = async (merchantId: string) => {
    const response = await fetch(`/api/prahsys/check-terminals?merchantId=${merchantId}`);
    if (!response.ok) {
      throw new Error('Failed to check terminals');
    }
    return response.json();
  };

  return (
    <PrahsysPaymentProvider
      config={{
        merchantId: "YOUR_MERCHANT_ID",
        onCreateSession: async (merchantId, data) => { /* ... */ },
        onProcessPayment: async (merchantId, data, paymentId) => { /* ... */ },

        // Add terminal check callback
        onCheckTerminals: checkTerminals,
      }}
    >
      <PaymentForm
        merchantId="YOUR_MERCHANT_ID"
        onCheckTerminals={checkTerminals}
        showTerminalOption={true}  // Enable terminal option
      />
    </PrahsysPaymentProvider>
  );
}
```

### Terminal Payment Flow

When a user selects terminal payment:

1. Form calls `onCheckTerminals` to get available terminals
2. `TerminalSelectorSection` displays list of ready terminals
3. User selects a terminal
4. User clicks "Pay Now"
5. Payment data is sent to your backend with terminal ID
6. Backend processes payment through selected terminal
7. Terminal prompts for card insertion/tap
8. Customer completes payment on terminal
9. Transaction completes and returns result

### Terminal Testing

**In Sandbox:**

* Terminal payments are auto-approved
* No physical terminal required for testing
* Terminal list returns mock terminals

**In Production:**

* Requires configured physical terminal
* Terminal must be powered on and connected
* Terminal must show "READY" status
* Customer physically interacts with terminal

<Callout icon="⚠️" theme="warning">
  **Important Terminal Notes**

  * Terminals must be configured in Prahsys Dashboard before use
  * Only terminals with "READY" status can process payments
  * Terminal connectivity issues will cause payment failures
  * Always provide manual card entry as a fallback option
  * Test terminal functionality thoroughly before production
</Callout>

***

## 4. Setup Payment Webhooks (Optional but Recommended)

Receive real-time notifications when payment events occur. Webhooks enable your application to stay synchronized with payment status changes without polling the API.

**Estimated Time**: 2-3 hours

### Tasks

* [ ] **Create webhook endpoint on your backend**
  * Route: `POST /api/webhooks/prahsys`
  * Accept POST requests with payment event data
  * Parse and validate webhook payload
  * Update payment status in your database

* [ ] **Register webhook endpoint in Prahsys Dashboard**
  * Navigate to [Prahsys Dashboard > Webhooks](https://dashboard.prahsys.com/denttracks/developers/webhooks)
  * Click "Add Endpoint"
  * Enter your webhook URL (e.g., `https://yourdomain.com/api/webhooks/prahsys`)
  * Save and verify endpoint is active

* [ ] **Subscribe to payment events**
  * Select events to receive:
    * `PAYMENT_SUCCEEDED` - Payment completed successfully
    * `PAYMENT_FAILED` - Payment declined or failed
    * `PAYMENT_REFUNDED` - Payment refunded
    * `PAYMENT_VOIDED` - Payment voided
  * Configure any filters if needed
  * Save event subscriptions

* [ ] **Implement webhook signature verification**
  * Use Svix library to verify webhook authenticity
  * Reject webhooks with invalid signatures
  * Prevent webhook spoofing attacks

* [ ] **Update payment status in your database**
  * Parse webhook payload for payment ID
  * Update payment record with new status
  * Trigger any necessary side effects (email, notifications, etc.)

* [ ] **Test webhook delivery**
  * Use Prahsys Dashboard to send test webhooks
  * Verify your endpoint receives and processes events
  * Check logs for any errors
  * Test idempotency (same webhook sent multiple times)

### Why Webhooks Matter

Webhooks provide several critical benefits:

1. **Real-time Updates** - Know immediately when payments succeed or fail
2. **No Polling Required** - Eliminate the need to constantly check payment status
3. **Reliable Delivery** - Svix ensures webhooks are delivered with automatic retries
4. **Audit Trail** - Complete history of all payment events in one place
5. **Async Processing** - Handle time-consuming operations after payment completion

**Without Webhooks:**

* Must poll API to check payment status
* Delayed notification of payment events
* Higher API usage and costs
* Risk of missing status changes

**With Webhooks:**

* Instant notification of all events
* Efficient and scalable
* Complete event history
* Reliable delivery with retries

### Understanding Webhooks

Webhooks are HTTP callbacks that Prahsys sends to your server when payment events occur. They use the [Svix](https://www.svix.com/) webhook delivery platform for enterprise-grade reliability.

**How Webhooks Work:**

1. **Event Occurs** - A payment succeeds, fails, or is refunded
2. **Prahsys Sends Webhook** - POST request to your endpoint
3. **Your Server Receives** - Endpoint handles the webhook
4. **Verification** - Validate webhook signature (security)
5. **Processing** - Update database, send notifications, etc.
6. **Response** - Return 200 OK to acknowledge receipt
7. **Retries** - If no response, Svix automatically retries

### Webhook Implementation

<Accordion title="Next.js Webhook Handler" icon="fa-code">
  ```typescript
  // File: app/api/webhooks/prahsys/route.ts

  import { NextRequest, NextResponse } from "next/server";
  import { Webhook } from "svix";
  import { db } from "@/lib/db";

  export async function POST(request: NextRequest) {
    try {
      // Get webhook payload
      const payload = await request.text();
      const headers = {
        "svix-id": request.headers.get("svix-id") || "",
        "svix-timestamp": request.headers.get("svix-timestamp") || "",
        "svix-signature": request.headers.get("svix-signature") || "",
      };

      // Verify webhook signature
      const webhook = new Webhook(process.env.PRAHSYS_WEBHOOK_SECRET!);
      let event;

      try {
        event = webhook.verify(payload, headers);
      } catch (err) {
        console.error("Webhook signature verification failed:", err);
        return NextResponse.json(
          { error: "Invalid signature" },
          { status: 401 }
        );
      }

      // Handle different event types
      const { type, data } = event;

      switch (type) {
        case "PAYMENT_SUCCEEDED":
          await handlePaymentSuccess(data);
          break;

        case "PAYMENT_FAILED":
          await handlePaymentFailure(data);
          break;

        case "PAYMENT_REFUNDED":
          await handlePaymentRefund(data);
          break;

        case "PAYMENT_VOIDED":
          await handlePaymentVoid(data);
          break;

        default:
          console.log(`Unhandled event type: ${type}`);
      }

      return NextResponse.json({ received: true });
    } catch (error) {
      console.error("Webhook error:", error);
      return NextResponse.json(
        { error: "Webhook processing failed" },
        { status: 500 }
      );
    }
  }

  async function handlePaymentSuccess(data: any) {
    const { paymentId, transactionId, amount, merchantId } = data;

    // Update payment status in database
    await db.payment.update({
      where: { id: paymentId },
      data: {
        status: "SUCCEEDED",
        transactionId,
        processedAt: new Date(),
      },
    });

    // Send confirmation email to customer
    // await sendPaymentConfirmationEmail(paymentId);

    console.log(`Payment ${paymentId} succeeded: $${amount}`);
  }

  async function handlePaymentFailure(data: any) {
    const { paymentId, reason } = data;

    await db.payment.update({
      where: { id: paymentId },
      data: {
        status: "FAILED",
        failureReason: reason,
        processedAt: new Date(),
      },
    });

    console.log(`Payment ${paymentId} failed: ${reason}`);
  }

  async function handlePaymentRefund(data: any) {
    const { paymentId, refundId, amount } = data;

    await db.payment.update({
      where: { id: paymentId },
      data: {
        status: "REFUNDED",
        refundId,
        refundedAt: new Date(),
      },
    });

    // Send refund confirmation email
    // await sendRefundConfirmationEmail(paymentId);

    console.log(`Payment ${paymentId} refunded: $${amount}`);
  }

  async function handlePaymentVoid(data: any) {
    const { paymentId } = data;

    await db.payment.update({
      where: { id: paymentId },
      data: {
        status: "VOIDED",
        voidedAt: new Date(),
      },
    });

    console.log(`Payment ${paymentId} voided`);
  }
  ```
</Accordion>

### Webhook Signature Verification

**Always verify webhook signatures** to ensure the webhook came from Prahsys and hasn't been tampered with.

Install Svix:

```bash
npm install svix
# or
pnpm add svix
```

Verification example:

```typescript
import { Webhook } from "svix";

const webhook = new Webhook(process.env.PRAHSYS_WEBHOOK_SECRET!);

try {
  const event = webhook.verify(payload, {
    "svix-id": request.headers.get("svix-id"),
    "svix-timestamp": request.headers.get("svix-timestamp"),
    "svix-signature": request.headers.get("svix-signature"),
  });

  // Webhook is verified - safe to process
} catch (err) {
  // Invalid signature - reject the webhook
  return res.status(401).json({ error: "Invalid signature" });
}
```

**See Svix docs:** [Verifying Webhook Signatures](https://docs.svix.com/receiving/verifying-payloads/how)

### Webhook Events

| Event Type          | Description                        | Payload Includes                                     |
| ------------------- | ---------------------------------- | ---------------------------------------------------- |
| `PAYMENT_SUCCEEDED` | Payment processed successfully     | paymentId, transactionId, amount, customer, merchant |
| `PAYMENT_FAILED`    | Payment declined or failed         | paymentId, reason, errorCode                         |
| `PAYMENT_REFUNDED`  | Payment refunded (full or partial) | paymentId, refundId, amount, originalAmount          |
| `PAYMENT_VOIDED`    | Payment voided before settlement   | paymentId, voidedAt                                  |

### Setup in Prahsys Dashboard

1. **Navigate to Webhooks:**
   * Go to [Prahsys Dashboard](https://dashboard.prahsys.com/)
   * Click "Developers" in sidebar
   * Click "Webhooks"

2. **Add Endpoint:**
   * Click "Add Endpoint" button
   * Enter your webhook URL: `https://yourdomain.com/api/webhooks/prahsys`
   * Select events to subscribe to
   * Click "Save"

3. **Get Webhook Secret:**
   * Copy the webhook signing secret
   * Add to your environment variables: `PRAHSYS_WEBHOOK_SECRET=whsec_...`

4. **Test Webhook:**
   * Click "Send Example" to send a test webhook
   * Verify your endpoint receives and processes it
   * Check webhook logs for delivery status

### Local Testing with Ngrok

During development, use [Ngrok](https://ngrok.com/) to test webhooks locally:

```bash
# Install Ngrok (if not already installed)
npm install -g ngrok

# Start your local server
npm run dev  # Running on localhost:3000

# In another terminal, start Ngrok
ngrok http 3000

# Ngrok will give you a public URL:
# https://abc123.ngrok.io

# Use this URL in Prahsys Dashboard:
# https://abc123.ngrok.io/api/webhooks/prahsys
```

**See detailed guide:** [Testing Webhooks Locally with Ngrok](doc:webhooksngrok)

### Idempotency Best Practices

Webhooks may be delivered multiple times (retries, network issues, etc.). Implement idempotency to handle duplicates:

```typescript
async function handlePaymentSuccess(data: any) {
  const { paymentId } = data;

  // Check if already processed
  const existing = await db.payment.findUnique({
    where: { id: paymentId },
  });

  if (existing?.status === "SUCCEEDED") {
    console.log(`Payment ${paymentId} already processed - skipping`);
    return; // Idempotent - safe to ignore duplicate
  }

  // Process the webhook
  await db.payment.update({
    where: { id: paymentId },
    data: { status: "SUCCEEDED" },
  });
}
```

<Callout icon="🔒" theme="warning">
  **Security Best Practices**

  * Always verify webhook signatures before processing
  * Never trust webhook data without verification
  * Use HTTPS endpoints only (no HTTP)
  * Implement idempotency to handle duplicate webhooks
  * Log all webhook events for debugging
  * Return 200 OK quickly to avoid retries
  * Process time-consuming tasks asynchronously
</Callout>

***

## 5. Handle Refunds and Voids (Optional)

Implement refund and void functionality to reverse payments when needed. Refunds return funds to customers after settlement, while voids cancel payments before they're settled.

**Estimated Time**: 1-2 days

### Tasks

* [ ] **Create refund endpoint**
  * Route: `POST /api/prahsys/refund`
  * Accepts: `{ merchantId, paymentId, amount?, reason? }`
  * Calls: `PrahsysGateway.transaction.refund()`
  * Returns: `RefundResponse`
  * Handle partial refunds (optional amount)

* [ ] **Create void endpoint**
  * Route: `POST /api/prahsys/void`
  * Accepts: `{ merchantId, paymentId, reason? }`
  * Calls: `PrahsysGateway.transaction.void()`
  * Returns: `VoidResponse`
  * Only works before settlement

* [ ] **Add refund/void UI to admin dashboard**
  * Create admin page to manage payments
  * Show payment status and refund/void options
  * Add confirmation dialogs
  * Display refund/void history

* [ ] **Test full and partial refunds**
  * Test full refund (entire amount)
  * Test partial refund (less than original amount)
  * Test multiple partial refunds up to total
  * Verify refund limits are enforced

* [ ] **Handle refund failures**
  * Display clear error messages
  * Handle "already refunded" errors
  * Handle "insufficient funds" errors
  * Provide retry mechanism

### Refund vs Void

Understanding the difference between refunds and voids is critical:

| Feature             | Void                                | Refund                          |
| ------------------- | ----------------------------------- | ------------------------------- |
| **Timing**          | Before settlement (same day)        | After settlement (next day+)    |
| **Effect**          | Cancels the transaction             | Returns funds to customer       |
| **Processing Time** | Immediate                           | 3-5 business days               |
| **Fees**            | No fees (transaction never settled) | Processing fees may still apply |
| **Amount**          | Full amount only                    | Full or partial amounts         |
| **Availability**    | Limited time window                 | Anytime after settlement        |

**When to Use Void:**

* Customer cancels order before settlement
* Duplicate charge detected immediately
* Error in transaction amount
* Within same business day

**When to Use Refund:**

* Customer returns product
* Service not delivered as promised
* Customer requests refund after settlement
* Partial refund for damaged goods

### Backend Implementation

<Accordion title="Refund Endpoint (Next.js)" icon="fa-code">
  ```typescript
  // File: app/api/prahsys/refund/route.ts

  import { NextRequest, NextResponse } from "next/server";
  import { PrahsysGateway } from "@/lib/services/prahsys-gateway/prahsys-gateway";
  import { db } from "@/lib/db";

  export async function POST(request: NextRequest) {
    try {
      const body = await request.json();
      const { merchantId, paymentId, amount, reason } = body;

      // Validate required fields
      if (!merchantId || !paymentId) {
        return NextResponse.json(
          { error: "merchantId and paymentId are required" },
          { status: 400 }
        );
      }

      // Optional: Check if payment exists and can be refunded
      const payment = await db.payment.findUnique({
        where: { id: paymentId },
      });

      if (!payment) {
        return NextResponse.json(
          { error: "Payment not found" },
          { status: 404 }
        );
      }

      if (payment.status === "REFUNDED") {
        return NextResponse.json(
          { error: "Payment already refunded" },
          { status: 400 }
        );
      }

      // Call Prahsys Gateway to refund
      const result = await PrahsysGateway.transaction.refund({
        merchantId,
        paymentId,
        apiKey: process.env.PRAHSYS_API_KEY!,
        data: {
          amount, // Optional - omit for full refund
          reason, // Optional - reason for refund
        },
      });

      if (result.isErr()) {
        console.error("Error refunding payment:", result.error);
        return NextResponse.json(
          { error: result.error.message },
          { status: 500 }
        );
      }

      // Update payment status in database
      await db.payment.update({
        where: { id: paymentId },
        data: {
          status: "REFUNDED",
          refundedAt: new Date(),
          refundAmount: amount || payment.amount,
          refundReason: reason,
        },
      });

      return NextResponse.json(result.value);
    } catch (error) {
      console.error("Unexpected error:", error);
      return NextResponse.json(
        { error: "Failed to refund payment" },
        { status: 500 }
      );
    }
  }
  ```
</Accordion>

<Accordion title="Void Endpoint (Next.js)" icon="fa-code">
  ```typescript
  // File: app/api/prahsys/void/route.ts

  import { NextRequest, NextResponse } from "next/server";
  import { PrahsysGateway } from "@/lib/services/prahsys-gateway/prahsys-gateway";
  import { db } from "@/lib/db";

  export async function POST(request: NextRequest) {
    try {
      const body = await request.json();
      const { merchantId, paymentId, reason } = body;

      // Validate required fields
      if (!merchantId || !paymentId) {
        return NextResponse.json(
          { error: "merchantId and paymentId are required" },
          { status: 400 }
        );
      }

      // Check if payment exists
      const payment = await db.payment.findUnique({
        where: { id: paymentId },
      });

      if (!payment) {
        return NextResponse.json(
          { error: "Payment not found" },
          { status: 404 }
        );
      }

      if (payment.status === "VOIDED") {
        return NextResponse.json(
          { error: "Payment already voided" },
          { status: 400 }
        );
      }

      // Check if void window has passed (usually same day)
      const paymentDate = new Date(payment.createdAt);
      const now = new Date();
      const hoursSincePayment = (now.getTime() - paymentDate.getTime()) / (1000 * 60 * 60);

      if (hoursSincePayment > 24) {
        return NextResponse.json(
          { error: "Void window has passed - use refund instead" },
          { status: 400 }
        );
      }

      // Call Prahsys Gateway to void
      const result = await PrahsysGateway.transaction.void({
        merchantId,
        paymentId,
        apiKey: process.env.PRAHSYS_API_KEY!,
        data: {
          reason, // Optional - reason for void
        },
      });

      if (result.isErr()) {
        console.error("Error voiding payment:", result.error);
        return NextResponse.json(
          { error: result.error.message },
          { status: 500 }
        );
      }

      // Update payment status in database
      await db.payment.update({
        where: { id: paymentId },
        data: {
          status: "VOIDED",
          voidedAt: new Date(),
          voidReason: reason,
        },
      });

      return NextResponse.json(result.value);
    } catch (error) {
      console.error("Unexpected error:", error);
      return NextResponse.json(
        { error: "Failed to void payment" },
        { status: 500 }
      );
    }
  }
  ```
</Accordion>

### Partial Refunds

Prahsys supports partial refunds - returning less than the original payment amount:

```typescript
// Full refund (entire amount)
await fetch('/api/prahsys/refund', {
  method: 'POST',
  body: JSON.stringify({
    merchantId: 'MERCHANT123',
    paymentId: 'PAYMENT-456',
    // No amount = full refund
  })
});

// Partial refund ($20 of $100 payment)
await fetch('/api/prahsys/refund', {
  method: 'POST',
  body: JSON.stringify({
    merchantId: 'MERCHANT123',
    paymentId: 'PAYMENT-456',
    amount: 20.00,  // Partial amount
    reason: 'Damaged item discount'
  })
});
```

**Partial Refund Rules:**

* Cannot exceed original payment amount
* Can issue multiple partial refunds up to total
* Track cumulative refunded amount
* Some gateways may limit number of partial refunds

### Admin UI Example

<Accordion title="Refund/Void Management Component" icon="fa-code">
  ```tsx
  // components/PaymentManagement.tsx

  import { useState } from 'react';

  interface Payment {
    id: string;
    amount: number;
    status: string;
    createdAt: string;
    customer: {
      email: string;
    };
  }

  export function PaymentManagement({ payment }: { payment: Payment }) {
    const [refundAmount, setRefundAmount] = useState(payment.amount);
    const [reason, setReason] = useState('');
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState<string | null>(null);

    const canVoid = () => {
      const paymentDate = new Date(payment.createdAt);
      const hoursSince = (Date.now() - paymentDate.getTime()) / (1000 * 60 * 60);
      return hoursSince < 24 && payment.status === 'SUCCEEDED';
    };

    const canRefund = () => {
      return payment.status === 'SUCCEEDED';
    };

    const handleVoid = async () => {
      if (!confirm('Are you sure you want to void this payment?')) return;

      setLoading(true);
      setError(null);

      try {
        const response = await fetch('/api/prahsys/void', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            merchantId: 'YOUR_MERCHANT_ID',
            paymentId: payment.id,
            reason,
          }),
        });

        if (!response.ok) {
          const data = await response.json();
          throw new Error(data.error || 'Failed to void payment');
        }

        alert('Payment voided successfully');
        window.location.reload();
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setLoading(false);
      }
    };

    const handleRefund = async () => {
      if (!confirm(`Refund $${refundAmount.toFixed(2)} to customer?`)) return;

      setLoading(true);
      setError(null);

      try {
        const response = await fetch('/api/prahsys/refund', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            merchantId: 'YOUR_MERCHANT_ID',
            paymentId: payment.id,
            amount: refundAmount,
            reason,
          }),
        });

        if (!response.ok) {
          const data = await response.json();
          throw new Error(data.error || 'Failed to refund payment');
        }

        alert('Refund initiated successfully');
        window.location.reload();
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setLoading(false);
      }
    };

    return (
      <div style={{ border: '1px solid #e5e7eb', borderRadius: '8px', padding: '1.5rem' }}>
        <h3>Payment {payment.id}</h3>
        <p>Amount: ${payment.amount.toFixed(2)}</p>
        <p>Status: {payment.status}</p>
        <p>Customer: {payment.customer.email}</p>

        {error && (
          <div style={{ color: '#ef4444', marginBottom: '1rem' }}>
            Error: {error}
          </div>
        )}

        <div style={{ marginTop: '1.5rem' }}>
          <label style={{ display: 'block', marginBottom: '0.5rem' }}>
            Reason (optional)
          </label>
          <input
            type="text"
            value={reason}
            onChange={(e) => setReason(e.target.value)}
            placeholder="Reason for refund/void"
            style={{ width: '100%', padding: '0.5rem', marginBottom: '1rem' }}
            disabled={loading}
          />

          {canRefund() && (
            <>
              <label style={{ display: 'block', marginBottom: '0.5rem' }}>
                Refund Amount
              </label>
              <input
                type="number"
                value={refundAmount}
                onChange={(e) => setRefundAmount(parseFloat(e.target.value))}
                max={payment.amount}
                step="0.01"
                style={{ width: '100%', padding: '0.5rem', marginBottom: '1rem' }}
                disabled={loading}
              />
            </>
          )}

          <div style={{ display: 'flex', gap: '1rem' }}>
            {canVoid() && (
              <button
                onClick={handleVoid}
                disabled={loading}
                style={{
                  padding: '0.75rem 1.5rem',
                  background: '#f59e0b',
                  color: 'white',
                  border: 'none',
                  borderRadius: '6px',
                  cursor: loading ? 'not-allowed' : 'pointer',
                }}
              >
                {loading ? 'Processing...' : 'Void Payment'}
              </button>
            )}

            {canRefund() && (
              <button
                onClick={handleRefund}
                disabled={loading}
                style={{
                  padding: '0.75rem 1.5rem',
                  background: '#ef4444',
                  color: 'white',
                  border: 'none',
                  borderRadius: '6px',
                  cursor: loading ? 'not-allowed' : 'pointer',
                }}
              >
                {loading ? 'Processing...' : `Refund $${refundAmount.toFixed(2)}`}
              </button>
            )}
          </div>
        </div>
      </div>
    );
  }
  ```
</Accordion>

### Testing Refunds and Voids

**In Sandbox:**

1. **Create a test payment:**
   ```bash
   # Use test card: 5123456789012346
   # Complete payment through your form
   ```

2. **Test void (within 24 hours):**
   ```bash
   curl -X POST http://localhost:3000/api/prahsys/void \
     -H "Content-Type: application/json" \
     -d '{
       "merchantId": "TESTMERCHANT123",
       "paymentId": "PAYMENT-456",
       "reason": "Test void"
     }'
   ```

3. **Test full refund:**
   ```bash
   curl -X POST http://localhost:3000/api/prahsys/refund \
     -H "Content-Type: application/json" \
     -d '{
       "merchantId": "TESTMERCHANT123",
       "paymentId": "PAYMENT-789",
       "reason": "Test refund"
     }'
   ```

4. **Test partial refund:**
   ```bash
   curl -X POST http://localhost:3000/api/prahsys/refund \
     -H "Content-Type: application/json" \
     -d '{
       "merchantId": "TESTMERCHANT123",
       "paymentId": "PAYMENT-789",
       "amount": 25.00,
       "reason": "Partial refund test"
     }'
   ```

<Callout icon="💡" theme="info">
  **Refund Processing Times**

  * **Voids**: Instant - transaction is cancelled before settlement
  * **Refunds**: 3-5 business days to appear in customer's account
  * **Partial Refunds**: Same processing time as full refunds
  * **Sandbox**: Both voids and refunds are instant for testing
</Callout>

***

## 6. Testing & Production Deployment

Comprehensive testing across all payment scenarios before production deployment. Ensure your integration handles success, failure, and edge cases correctly.

**Estimated Time**: 2-3 days

### Tasks

* [ ] **Test successful payment scenarios**
  * Mastercard successful payment
  * Visa successful payment
  * With customer details
  * With billing details
  * Minimum amount ($0.50)
  * Large amount ($9,999.99)

* [ ] **Test payment failure scenarios**
  * Declined card (insufficient funds)
  * Invalid card number
  * Expired card
  * Invalid CVV
  * Network timeout simulation

* [ ] **Test validation errors**
  * Missing required fields
  * Invalid amount format
  * Invalid email format
  * Card field errors
  * Session expiration

* [ ] **Test sandbox vs production environments**
  * Verify TEST prefix works in sandbox
  * Verify production merchant ID works
  * Test environment switching
  * Verify API key isolation

* [ ] **Add payment status UI indicators**
  * Payment history page
  * Status badges (SUCCEEDED, FAILED, REFUNDED)
  * Payment details view
  * Real-time status updates (with webhooks)

* [ ] **Create production deployment checklist**
  * Switch to LIVE API key
  * Remove TEST prefix from merchant ID
  * Enable production webhooks
  * Configure error tracking
  * Set up monitoring and alerts

### Testing Checklist

#### ✅ Successful Payment Scenarios

Test that payments complete successfully with valid data:

| Scenario                         | Test Card        | Amount    | Expected Result             |
| -------------------------------- | ---------------- | --------- | --------------------------- |
| Mastercard Standard              | 5123456789012346 | $10.00    | APPROVED                    |
| Visa Standard                    | 4111111111111111 | $25.00    | APPROVED                    |
| Mastercard With Customer Details | 5123456789012346 | $50.00    | APPROVED with customer data |
| Visa With Billing Address        | 4111111111111111 | $75.00    | APPROVED with billing data  |
| Minimum Amount                   | 5123456789012346 | $0.50     | APPROVED                    |
| Large Amount                     | 5123456789012346 | $9,999.99 | APPROVED                    |
| Terminal Payment                 | N/A              | $100.00   | APPROVED via terminal       |

**Test Steps:**

1. Enter payment amount and description
2. (Optional) Fill customer details
3. (Optional) Fill billing address
4. Enter test card details
5. Click "Pay Now"
6. Verify payment succeeds
7. Check payment appears in Prahsys Dashboard
8. Verify webhook received (if configured)

#### ❌ Failure Scenarios

Test that failures are handled gracefully:

| Scenario            | Test Card                       | Expected Result                |
| ------------------- | ------------------------------- | ------------------------------ |
| Declined Card       | 5100000000000000                | DECLINED - Insufficient funds  |
| Invalid Card Number | 1234567890123456                | Validation error before submit |
| Expired Card        | 5123456789012346 (exp: 01/2020) | DECLINED - Expired card        |
| Invalid CVV         | 5123456789012346 (CVV: 999)     | Field validation error         |

**Test Steps:**

1. Enter invalid/declined card data
2. Submit payment
3. Verify clear error message shown
4. Verify form remains editable
5. Verify no payment created in Dashboard
6. Verify error logged appropriately

#### ⚠️ Edge Cases and Validation

| Scenario           | Input        | Expected Result       |
| ------------------ | ------------ | --------------------- |
| Missing Amount     | Empty        | Form validation error |
| Negative Amount    | -10.00       | Form validation error |
| Zero Amount        | 0.00         | Form validation error |
| Invalid Email      | "notanemail" | Form validation error |
| Empty Card Fields  | All empty    | Session update error  |
| Session Expiration | Wait 30+ min | Create new session    |

#### 🔄 Environment Testing

| Test               | Sandbox                | Production             |
| ------------------ | ---------------------- | ---------------------- |
| Merchant ID Format | TESTMERCHANT123        | MERCHANT123            |
| API Key            | TEST key               | LIVE key               |
| Test Cards         | Work                   | Declined               |
| Real Cards         | Declined               | Work                   |
| Webhook URL        | ngrok or test endpoint | Production endpoint    |
| Terminal Payments  | Auto-approved          | Requires real terminal |

### Test Card Numbers

Use these test cards in **sandbox only**:

| Card Type                 | Number           | CVV | Expiry  | Result   |
| ------------------------- | ---------------- | --- | ------- | -------- |
| Mastercard (Success)      | 5123456789012346 | 123 | 12/2025 | Approved |
| Visa (Success)            | 4111111111111111 | 123 | 12/2025 | Approved |
| Mastercard (Declined)     | 5100000000000000 | 123 | 12/2025 | Declined |
| Visa (Insufficient Funds) | 4000000000000002 | 123 | 12/2025 | Declined |

<Callout icon="⚠️" theme="error">
  **Never Use Real Cards in Sandbox**

  * Real card numbers will be declined in sandbox
  * Test cards will be declined in production
  * Never commit test card numbers to source control
  * Use environment-specific testing procedures
</Callout>

### Production Deployment Checklist

Before deploying to production:

#### Environment Configuration

* [ ] Switch from TEST API key to LIVE API key
* [ ] Remove TEST prefix from merchant ID
* [ ] Update environment variables in production
* [ ] Verify HTTPS is enforced on all endpoints
* [ ] Configure production webhook endpoint
* [ ] Test webhook delivery in production
* [ ] Verify webhook signature validation

#### Security

* [ ] API keys stored in secure environment variables
* [ ] Never commit credentials to git
* [ ] HTTPS enforced on all pages
* [ ] Webhook signature verification enabled
* [ ] Rate limiting configured
* [ ] Error messages don't expose sensitive data
* [ ] Logging configured (no card data logged)

#### Monitoring

* [ ] Error tracking configured (Sentry, etc.)
* [ ] Payment success/failure metrics
* [ ] Webhook delivery monitoring
* [ ] API response time monitoring
* [ ] Alert rules for payment failures
* [ ] Dashboard for payment analytics

#### Testing

* [ ] Test successful payment with real card
* [ ] Test declined payment
* [ ] Verify webhook delivery
* [ ] Test refund flow (if implemented)
* [ ] Verify email notifications (if configured)
* [ ] Load test with expected traffic
* [ ] Test on mobile devices
* [ ] Cross-browser testing

#### Documentation

* [ ] Internal runbook for payment issues
* [ ] Customer support scripts for common issues
* [ ] Refund/void procedures documented
* [ ] Contact information for Prahsys support
* [ ] Rollback plan if issues occur

### Payment Status UI

Add payment status indicators to your admin dashboard:

<Accordion title="Payment Status Badge Component" icon="fa-code">
  ```tsx
  // components/PaymentStatusBadge.tsx

  interface PaymentStatusBadgeProps {
    status: 'PENDING' | 'SUCCEEDED' | 'FAILED' | 'REFUNDED' | 'VOIDED';
  }

  export function PaymentStatusBadge({ status }: PaymentStatusBadgeProps) {
    const styles = {
      PENDING: {
        background: '#fef3c7',
        color: '#92400e',
        icon: '🕐',
      },
      SUCCEEDED: {
        background: '#d1fae5',
        color: '#065f46',
        icon: '✓',
      },
      FAILED: {
        background: '#fee2e2',
        color: '#991b1b',
        icon: '✗',
      },
      REFUNDED: {
        background: '#dbeafe',
        color: '#1e40af',
        icon: '↩',
      },
      VOIDED: {
        background: '#e5e7eb',
        color: '#374151',
        icon: '⊘',
      },
    };

    const style = styles[status];

    return (
      <span
        style={{
          display: 'inline-flex',
          alignItems: 'center',
          gap: '0.5rem',
          padding: '0.25rem 0.75rem',
          borderRadius: '9999px',
          fontSize: '0.875rem',
          fontWeight: 600,
          background: style.background,
          color: style.color,
        }}
      >
        <span>{style.icon}</span>
        <span>{status}</span>
      </span>
    );
  }
  ```

  Usage:

  ```tsx
  <PaymentStatusBadge status="SUCCEEDED" />
  <PaymentStatusBadge status="FAILED" />
  <PaymentStatusBadge status="REFUNDED" />
  ```
</Accordion>

### Common Issues and Solutions

| Issue                                   | Cause                                 | Solution                             |
| --------------------------------------- | ------------------------------------- | ------------------------------------ |
| "Failed to load Prahsys payment script" | Script URL incorrect                  | Verify merchant ID and TEST prefix   |
| "Session ID not found"                  | Backend failed to create session      | Check API key and backend logs       |
| "Card number invalid"                   | Iframe not loaded or wrong element ID | Verify field IDs match configuration |
| "Payment session not ready"             | Fields not configured yet             | Wait for `isReady` state             |
| "Webhook signature invalid"             | Wrong webhook secret                  | Verify PRAHSYS_WEBHOOK_SECRET        |
| "Merchant not found"                    | Wrong merchant ID                     | Check merchant ID and environment    |
| "Payment already refunded"              | Duplicate refund attempt              | Check payment status first           |

### Performance Considerations

**Script Loading:**

* Payment session script loads only when needed
* Script is cached by browser after first load
* Total script size: ~50KB

**Session Creation:**

* Typical response time: 200-500ms
* Sessions expire after 30 minutes
* Auto-recreate on expiration

**Payment Processing:**

* Card-present (terminal): 3-8 seconds
* Card-not-present (manual): 2-5 seconds
* Includes card validation and settlement

**Recommendations:**

* Show loading states during session creation
* Cache terminal list (refresh every 5 minutes)
* Implement optimistic UI updates with webhooks
* Add retry logic for network failures

***

## Payment Processing Flow Diagram

<Image align="center" border={true} caption="Complete payment processing flow showing client, server, and Prahsys Gateway interaction" src="https://files.readme.io/payment-flow-diagram.png" />

**Step-by-Step Flow:**

1. **Customer visits payment page** → React app renders PaymentForm
2. **Provider requests session** → Calls your backend `/api/prahsys/create-session`
3. **Backend creates session** → Calls Prahsys API with merchant ID
4. **Prahsys returns session ID** → Backend forwards to client
5. **Client configures fields** → Secure iframes load for card entry
6. **Customer enters card details** → Data stays in iframes (never touches your server)
7. **Customer clicks "Pay Now"** → Client calls `updateSessionFromForm()`
8. **Card data validated** → Prahsys validates and stores in session
9. **Client calls payment endpoint** → POST to `/api/prahsys/process-payment`
10. **Backend processes payment** → Calls Prahsys API with session ID
11. **Prahsys processes transaction** → Charges card through payment gateway
12. **Result returned to backend** → Payment success or failure
13. **Backend returns to client** → Client shows success/error message
14. **(Optional) Webhook sent** → Prahsys sends webhook to your server
15. **Database updated** → Your server updates payment status

***

## Troubleshooting

### Script Loading Issues

**Problem:** "Failed to load Prahsys payment script"

**Solutions:**

* Verify merchant ID is correct
* Add TEST prefix in sandbox mode
* Check browser console for script errors
* Verify internet connectivity
* Try clearing browser cache

### Session Creation Failures

**Problem:** Session creation returns 401 or 500 error

**Solutions:**

* Verify API key is correct (TEST vs LIVE)
* Check API key has proper permissions
* Ensure merchant is approved
* Check backend error logs
* Verify merchant ID format

### Card Field Issues

**Problem:** Card fields not appearing or not editable

**Solutions:**

* Verify element IDs match configuration (#card-number, etc.)
* Ensure session ID is valid
* Wait for `isReady` state before showing form
* Check browser console for iframe errors
* Verify no ad blockers are interfering

### Payment Processing Failures

**Problem:** Payments fail with unclear errors

**Solutions:**

* Check payment data format (amount as number, not string)
* Verify session ID is included in request
* Ensure session hasn't expired (30 min limit)
* Test with known-good test card
* Check Prahsys Dashboard for decline reason

### Webhook Issues

**Problem:** Webhooks not being received

**Solutions:**

* Verify webhook URL is publicly accessible
* Check webhook signature verification code
* Ensure endpoint returns 200 OK
* Check webhook logs in Prahsys Dashboard
* Test with ngrok for local development
* Verify firewall allows incoming requests

### Environment Issues

**Problem:** Production merchant works in sandbox (or vice versa)

**Solutions:**

* Use TEST prefix only in sandbox
* Verify API key matches environment
* Check merchant status is APPROVED
* Use correct test cards for environment
* Verify webhook URLs are environment-specific

***

## Production Checklist

### Pre-Launch

* [ ] All tests passing (success, failure, edge cases)
* [ ] Error handling implemented and tested
* [ ] Logging configured (no sensitive data logged)
* [ ] API keys securely stored in environment variables
* [ ] HTTPS enforced on all pages and APIs
* [ ] Rate limiting configured
* [ ] Webhook signature verification enabled
* [ ] Email notifications configured
* [ ] Customer support scripts prepared
* [ ] Rollback plan documented

### Launch Day

* [ ] Switch to LIVE API key
* [ ] Remove TEST prefix from merchant ID
* [ ] Update webhook endpoint to production URL
* [ ] Verify webhooks are received
* [ ] Test successful payment with real card
* [ ] Monitor error tracking dashboard
* [ ] Check payment appears in Prahsys Dashboard
* [ ] Verify customer confirmations sent
* [ ] Team on standby for issues

### Post-Launch Monitoring

* [ ] Monitor payment success rate
* [ ] Track average payment processing time
* [ ] Review error logs daily
* [ ] Monitor webhook delivery rate
* [ ] Check customer support tickets
* [ ] Review refund/void requests
* [ ] Analyze payment failure reasons
* [ ] Performance optimization if needed

***

## Additional Resources

**API Documentation:**

* [Pay Session Documentation](https://docs.prahsys.com/docs/pay-session)
* [Payment API Reference](ref:payment-api)
* [Webhook Events](doc:webhooks)
* [Error Codes Reference](doc:error-codes)

**Prahsys Dashboard:**

* [Dashboard Home](https://dashboard.prahsys.com/)
* [API Keys](https://dashboard.prahsys.com/denttracks/developers/api-keys)
* [Webhooks](https://dashboard.prahsys.com/denttracks/developers/webhooks)
* [Payment History](https://dashboard.prahsys.com/denttracks/payments)

**Component Examples:**

* React Components: `/examples/react/unstyled/PrahsysPaymentComponents/`
* Backend Examples: `/examples/react/unstyled/backend-examples/`
* Usage Examples: `/examples/react/unstyled/usage-examples/`
* Styling Examples: `/examples/react/unstyled/styling-examples/`

**Support:**

* Slack: #denttracks-support
* Email: [support@prahsys.com](mailto:support@prahsys.com)
* Phone: 1-800-PRAHSYS

***

## Summary

You've successfully completed payment processing integration! Your application can now:

✅ Accept secure card payments via Pay Session
✅ Process payments without handling sensitive card data
✅ Support both online (card-not-present) and terminal (card-present) payments
✅ Receive real-time payment status updates via webhooks
✅ Handle refunds and voids for payment reversals
✅ Test thoroughly across success and failure scenarios
✅ Deploy confidently to production with proper monitoring

**Next Steps:**

1. Monitor payment success rates and errors
2. Optimize user experience based on customer feedback
3. Consider adding saved payment methods for returning customers
4. Implement subscription/recurring payments if needed
5. Review payment analytics in Prahsys Dashboard

**Questions or Issues?**

* Contact Prahsys support via Slack or email
* Review the troubleshooting section above
* Check API documentation for advanced features
* Share feedback to help improve this integration

Thank you for integrating with Prahsys Gateway! 🎉
