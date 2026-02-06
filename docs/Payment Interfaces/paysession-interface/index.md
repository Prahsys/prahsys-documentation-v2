---
title: PaySession
excerpt: >-
  After loading the PaySession script, the PaySession object is loaded to the
  window. Here is the interface for the PaySession.
deprecated: false
hidden: false
metadata:
  robots: index
---
<Recipe slug="pay-session-integration" title="Pay Session" />

## PaySession Interface

After loading the PaySession object into the window with the PaySession Script:

```html PaySession Script
<script src="https://gateway.prahsys.com/n1/merchant/{merchantId}/session/{sessionId}/script.js"></script>
```

<Callout icon="❗️" theme="error">
  When working with a SANDBOX merchant and loading PaySession or PayPortal script, you must use your test API key to create the session (sk_test_123...)
</Callout>

```typescript PaySession Interface
/**
 * Global PaymentSession object available after loading the session.js script
 * @example
 * <script src="https://gateway.prahsys.com/n1/merchant/{merchantId}/session/{sessionId}/script.js"></script>
 */
declare global {
  interface Window {
    PaymentSession: PaySession;
  }
}

// ============================================================================
// Main PaySession Interface
// ============================================================================

/**
 * The PaySession object provides methods for securely collecting
 * payment details through hosted fields
 */
export interface PaySession {
  /**
   * Initialize the hosted session interaction
   * @param config - Configuration object for the session
   * @example
   * PaymentSession.configure({
   *   fields: {
   *     card: {
   *       number: "#card-number",
   *       securityCode: "#security-code",
   *       expiryMonth: "#expiry-month",
   *       expiryYear: "#expiry-year",
   *       nameOnCard: "#cardholder-name"
   *     }
   *   },
   *   callbacks: {
   *     initialized: (response) => console.log("Initialized"),
   *     formSessionUpdate: (response) => console.log("Updated")
   *   }
   * });
   */
  configure(config: PaySessionConfig): void;

  /**
   * Store hosted field input data into the session
   * @param namespace - The payment method namespace (e.g., "card")
   * @example PaymentSession.updateSessionFromForm("card");
   */
  updateSessionFromForm(namespace: string): void;

  /**
   * Set CSS styles for when fields are in focus
   * @param fields - Array of field identifiers
   * @param styles - CSS styles to apply
   * @example
   * PaymentSession.setFocusStyle(
   *   ["card.number", "card.securityCode"],
   *   { backgroundColor: "#f3f4f6", fontWeight: "bold" }
   * );
   */
  setFocusStyle(fields: string[], styles: FieldStyles): void;

  /**
   * Set CSS styles for when fields are being hovered
   * @param fields - Array of field identifiers
   * @param styles - CSS styles to apply
   * @example
   * PaymentSession.setHoverStyle(
   *   ["card.number", "card.securityCode"],
   *   { backgroundColor: "#f3f4f6" }
   * );
   */
  setHoverStyle(fields: string[], styles: FieldStyles): void;

  /**
   * Set CSS styles for field placeholders
   * @param fields - Array of field identifiers
   * @param styles - CSS styles to apply
   * @example
   * PaymentSession.setPlaceholderStyle(
   *   ["card.number"],
   *   { color: "#9ca3af" }
   * );
   */
  setPlaceholderStyle(fields: string[], styles: FieldStyles): void;

  /**
   * Set CSS styles for fields with validation errors
   * @param fields - Array of field identifiers
   * @param styles - CSS styles to apply
   * @example
   * PaymentSession.setErrorStyle(
   *   ["card.number", "card.securityCode"],
   *   { borderColor: "#ef4444", backgroundColor: "#fef2f2" }
   * );
   */
  setErrorStyle(fields: string[], styles: FieldStyles): void;

  /**
   * Set CSS styles for fields with valid input
   * @param fields - Array of field identifiers
   * @param styles - CSS styles to apply
   * @example
   * PaymentSession.setValidStyle(
   *   ["card.number"],
   *   { borderColor: "#10b981" }
   * );
   */
  setValidStyle(fields: string[], styles: FieldStyles): void;

  /**
   * Programmatically set the state of one or more fields
   * @param fields - Array of field identifiers
   * @param state - State to apply ('error', 'valid', or null to clear)
   * @example
   * PaymentSession.setFieldState(["card.number"], "error");
   * PaymentSession.setFieldState(["card.number"], null); // Clear state
   */
  setFieldState(fields: string[], state: string | null): void;
}

// ============================================================================
// Response Interfaces
// ============================================================================

/**
 * Generic response structure from PaySession operations
 */
export interface PaySessionResponse {
  status?: "ok" | "error" | "system_error";
  message?: string;
  [key: string]: unknown;
}

/**
 * Response returned when updating session from form fields
 */
export interface FormSessionUpdateResponse {
  status?: "ok" | "fields_in_error" | "request_timeout" | "system_error";
  errors?: {
    [fieldName: string]: string;
  };
  session?: {
    id: string;
    version?: number;
  };
  [key: string]: unknown;
}

// ============================================================================
// Style Interfaces
// ============================================================================

/**
 * CSS styles that can be applied to hosted payment fields
 */
export interface FieldStyles {
  backgroundColor?: string;
  color?: string;
  fontFamily?: string;
  fontSize?: string;
  fontWeight?: string | number;
  fontStyle?: string;
  lineHeight?: string | number;
  textAlign?: "left" | "right" | "center";
  textDecoration?: string;
  border?: string;
  borderColor?: string;
  borderWidth?: string;
  borderStyle?: string;
  borderRadius?: string;
  boxShadow?: string;
  outline?: string;
  padding?: string;
  margin?: string;
  width?: string;
  height?: string;
  opacity?: string;
  [key: string]: unknown; // Allow any additional CSS properties
}

// ============================================================================
// Field Configuration
// ============================================================================

/**
 * Configuration for credit/debit card payment fields
 */
export interface CardFieldConfig {
  /**
   * CSS selector for the card number field
   * @example "#card-number"
   */
  number?: string;

  /**
   * CSS selector for the security code (CVV/CVC) field
   * @example "#security-code"
   */
  securityCode?: string;

  /**
   * CSS selector for the expiry month field
   * @example "#expiry-month"
   */
  expiryMonth?: string;

  /**
   * CSS selector for the expiry year field
   * @example "#expiry-year"
   */
  expiryYear?: string;

  /**
   * CSS selector for the cardholder name field
   * @example "#cardholder-name"
   */
  nameOnCard?: string;
}

/**
 * Payment fields configuration
 * Currently supports card payments only
 */
export interface PaySessionFields {
  /**
   * Credit/debit card field configuration
   */
  card?: CardFieldConfig;
}

// ============================================================================
// Callback Interfaces
// ============================================================================

/**
 * Event callbacks for PaySession interactions
 */
export interface PaySessionCallbacks {
  /**
   * Called when the PaySession has been successfully initialized
   * @param response - Initialization response
   */
  initialized?: (response: PaySessionResponse) => void;

  /**
   * Called after attempting to update the session with form data
   * @param response - Update response with status and any errors
   */
  formSessionUpdate?: (response: FormSessionUpdateResponse) => void;
}

// ============================================================================
// Configuration Interface
// ============================================================================

/**
 * Configuration object for initializing PaySession
 */
export interface PaySessionConfig {
  /**
   * Session ID (optional - already embedded in script URL)
   * If provided, will be validated against the session ID from the script URL.
   * @example "SESSION0002776555072F1187121H61"
   */
  session?: string;

  /**
   * Field mappings for payment methods
   */
  fields: PaySessionFields;

  /**
   * Clickjacking mitigation options
   * @example ["javascript"]
   */
  frameEmbeddingMitigation?: string[];

  /**
   * Event callbacks for session interactions
   */
  callbacks?: PaySessionCallbacks;

  /**
   * Allow additional configuration properties
   */
  [key: string]: unknown;
}

```
