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

<Recipe slug="pay-portal-integration" title="Pay Portal" />

## PayPortal Checkout Interface

After loading the PaySession object into the window with the PaySession Script:

```html PaySession Script
<script src="https://secure.prahsys.com/static/checkout/checkout.min.js"></script>
```

```typescript Checkout Interface
/**
 * Error response when payment initiation fails
 */
export interface CheckoutError {
  /**
   * Error code identifying the type of error
   */
  code?: string;

  /**
   * Human-readable error message
   */
  message?: string;

  /**
   * Additional error details
   */
  details?: string;

  /**
   * HTTP status code if applicable
   */
  status?: number;
}

/**
 * Response returned upon successful payment completion
 */
export interface CheckoutCompleteResponse {
  /**
   * Session ID associated with the payment
   */
  sessionId?: string;

  /**
   * Order ID for the transaction
   */
  orderId?: string;

  /**
   * Transaction ID from the gateway
   */
  transactionId?: string;

  /**
   * Payment result code
   */
  result?: string;

  /**
   * Payment status
   */
  status?: "SUCCESS" | "PENDING" | "FAILED";

  /**
   * Additional response data
   */
  [key: string]: unknown;
}

// ============================================================================
// Configuration Types
// ============================================================================

/**
 * Merchant address information
 */
export interface MerchantAddress {
  /**
   * Street address line 1
   */
  line1?: string;

  /**
   * Street address line 2
   */
  line2?: string;

  /**
   * City name
   */
  city?: string;

  /**
   * State or province code
   */
  stateProvince?: string;

  /**
   * Postal or ZIP code
   */
  postcodeZip?: string;

  /**
   * Country code (ISO 3166-1 alpha-2)
   * @example "US"
   */
  country?: string;
}

/**
 * Display control options for customizing the checkout UI
 */
export interface DisplayControl {
  /**
   * Color scheme for the checkout interface
   * @example "dark" | "light"
   */
  theme?: string;

  /**
   * Primary brand color in hex format
   * @example "#0066CC"
   */
  brandColor?: string;

  /**
   * Logo URL to display in the checkout
   */
  logoUrl?: string;

  /**
   * Whether to show the order summary
   * @default true
   */
  showOrderSummary?: boolean;

  /**
   * Whether to show billing address fields
   * @default true
   */
  showBillingAddress?: boolean;

  /**
   * Whether to show shipping address fields
   * @default false
   */
  showShippingAddress?: boolean;
}

/**
 * Order information for the checkout
 */
export interface OrderInfo {
  /**
   * Unique order identifier
   * @example "ORDER-123456"
   */
  id?: string;

  /**
   * Total amount for the order
   */
  amount?: number | string;

  /**
   * Currency code (ISO 4217)
   * @example "USD"
   */
  currency?: string;

  /**
   * Order description
   */
  description?: string;

  /**
   * Order reference number
   */
  reference?: string;
}

/**
 * Interaction configuration for checkout behavior
 */
export interface CheckoutInteraction {
  /**
   * Merchant information displayed during checkout
   */
  merchant?: {
    /**
     * Merchant business name
     */
    name?: string;

    /**
     * Merchant address details
     */
    address?: MerchantAddress;

    /**
     * Merchant contact email
     */
    email?: string;

    /**
     * Merchant contact phone
     */
    phone?: string;
  };

  /**
   * Display customization options
   */
  displayControl?: DisplayControl;

  /**
   * Checkout timeout duration in seconds
   * @default 1800 (30 minutes)
   */
  timeout?: number;

  /**
   * URL to redirect to on timeout
   */
  timeoutUrl?: string;

  /**
   * URL to redirect to when customer cancels payment
   */
  cancelUrl?: string;

  /**
   * URL to return to after payment completion
   */
  returnUrl?: string;

  /**
   * Operation type for the transaction
   * @example "PURCHASE" | "AUTHORIZE"
   */
  operation?: string;

  /**
   * Locale for the checkout interface
   * @example "en_US"
   */
  locale?: string;

  /**
   * Additional interaction parameters
   */
  [key: string]: unknown;
}

/**
 * Event callbacks for Checkout interactions
 */
export interface CheckoutCallbacks {
  /**
   * Triggered when payment initiation fails
   * @param error - Error details
   */
  error?: (error: CheckoutError) => void;

  /**
   * Triggered upon successful payment completion
   * @param response - Payment completion response
   */
  complete?: (response: CheckoutCompleteResponse) => void;

  /**
   * Activated when payers abandon payment (payment page only)
   * User navigates away or clicks cancel button
   */
  cancel?: () => void;

  /**
   * Triggered if payment isn't completed within the allowed timeframe
   */
  timeout?: () => void;

  /**
   * Executes before browser navigation away from the page
   * Use this to save form state if needed
   */
  beforeRedirect?: () => void;

  /**
   * Executes when the browser returns post-redirect
   * Use this to restore form state if needed
   */
  afterRedirect?: () => void;
}

/**
 * Configuration object for initializing Checkout
 */
export interface CheckoutConfig {
  /**
   * Session information
   */
  session: {
    /**
     * The session ID obtained from the Initiate Checkout endpoint
     * @example "SESSION0002776555072F1187121H61"
     */
    id: string;

    /**
     * API version to use
     * @default "100"
     */
    version?: string;
  };

  /**
   * Interaction configuration for checkout behavior
   */
  interaction?: CheckoutInteraction;

  /**
   * Order information
   */
  order?: OrderInfo;

  /**
   * Event callbacks
   */
  callbacks?: CheckoutCallbacks;

  /**
   * Allow additional configuration properties
   */
  [key: string]: unknown;
}

// ============================================================================
// Main Checkout Interface
// ============================================================================

/**
 * The Checkout object provides methods for integrating payment flows
 * into merchant websites through embedded forms or hosted payment pages
 */
export interface Checkout {
  /**
   * Prepare the library before payment
   * Must be called before using other Checkout methods
   * @param config - Configuration object for the checkout session
   * @example
   * Checkout.configure({
   *   session: { id: sessionId },
   *   interaction: {
   *     merchant: { name: "My Store" },
   *     displayControl: { theme: "light" }
   *   },
   *   callbacks: {
   *     complete: (response) => console.log("Payment complete", response),
   *     error: (error) => console.error("Payment error", error)
   *   }
   * });
   */
  configure(config: CheckoutConfig): void;

  /**
   * Displays a hosted payment form directly on the merchant's site
   * The form is embedded within a container element on your page
   * @param containerId - CSS selector or element ID for the container
   * @example
   * // Show embedded checkout form in a div with id="checkout-container"
   * Checkout.showEmbeddedPage("#checkout-container");
   */
  showEmbeddedPage(containerId: string): void;

  /**
   * Redirects users to a hosted payment page
   * The user will leave your site and complete payment on Mastercard's hosted page
   * @example
   * // Redirect to full-page checkout
   * Checkout.showPaymentPage();
   */
  showPaymentPage(): void;

  /**
   * Default implementation for beforeRedirect callback functionality
   * Saves form field values to session storage before redirect
   * Can be used to preserve form state across page navigations
   * @example
   * Checkout.saveFormFields();
   */
  saveFormFields(): void;

  /**
   * Default implementation for afterRedirect callback functionality
   * Restores form field values from session storage after redirect
   * Can be used to restore form state after returning from payment page
   * @example
   * Checkout.restoreFormFields();
   */
  restoreFormFields(): void;
}

/**
 * Global Checkout object available after loading the checkout.min.js script
 * @example
 * <script src="https://na-gateway.mastercard.com/static/checkout/checkout.min.js"></script>
 */
declare global {
  interface Window {
    Checkout: Checkout;
  }
}

```
