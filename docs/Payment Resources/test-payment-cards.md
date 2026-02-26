---
title: Test Payment Cards
excerpt: >-
  This guide will provide you with all you need to know about testing with
  credit cards. We'll cover the different types of credit cards, how to use
  them, and what their respective responses mean.
deprecated: false
hidden: false
metadata:
  title: Test Cards for Payment Testing | Prahsys Documentation
  description: >-
    Complete reference guide for Prahsys Payments test credit card numbers,
    including Visa, Mastercard, American Express, and Discover cards with
    various response scenarios for thorough payment integration testing.
  keywords:
    - payment test cards
    - credit card testing
    - Prahsys test cards
    - AVS testing
    - CVV testing
    - payment gateway testing
    - transaction response testing
    - Visa test cards
    - Mastercard test cards
    - American Express test cards
    - Discover test cards
  robots: index
---
## Overview

Simulated transactions use dedicated **card numbers** to control transaction outcomes.

- Each test card number maps to a specific outcome
- Any unrecognized card number which passes LUHN validation is treated as a **success**
- Use any future expiration date, any CVV, and any billing address
- Amount and CVV values have no effect on the outcome

Source: `App\Data\Api\N1\Billing\TestCardEnum`

## Success Cards

Use any of these cards to get an approved transaction.

| Brand      | Card Number        | TestCardEnum |
|------------|--------------------|--------------|
| Visa       | 4242424242424242   | VISA         |
| Visa Debit | 4000056655665556   | VISA_DEBIT   |
| Mastercard | 5555555555554444   | MASTERCARD   |
| Amex       | 378282246310005    | AMEX         |
| Discover   | 6011111111111117   | DISCOVER     |

Any card number not listed on this page (success or error) is also treated as success. Brand is auto-detected from the BIN pattern.

## Error Cards

These cards simulate specific failure scenarios. Each card maps through a processing pipeline that produces a `result` and an `errorCode` in the API response.

### Decline Cards (card/issuer rejected the transaction)

| Scenario          | Card Number        | Result      | Error Code                   | Payment Status |
|-------------------|--------------------|-------------|------------------------------|----------------|
| Generic Decline   | 4000000000000002   | DECLINED    | TRANSACTION_DECLINED         | DECLINED       |
| Insufficient Funds| 4000000000009995   | DECLINED    | TRANSACTION_DECLINED         | DECLINED       |
| Card Expired      | 4000000000000069   | DECLINED    | CARD_EXPIRED                 | DECLINED       |
| Invalid Card      | 4000000000000127   | DECLINED    | INVALID_CARD_NUMBER          | DECLINED       |
| CSC Mismatch      | 4000000000000101   | DECLINED    | CSC_VERIFICATION_FAILED      | DECLINED       |
| AVS Mismatch      | 4000000000000010   | DECLINED    | ADDRESS_VERIFICATION_FAILED  | DECLINED       |

### System Error Cards (infrastructure/processor-level failures)

| Scenario      | Card Number        | Result  | Error Code          | Payment Status |
|---------------|--------------------|---------|---------------------|----------------|
| Timeout       | 4000000000009987   | UNKNOWN | TRANSACTION_FAILED  | FAILED         |
| Network Error | 4000000000000119   | ERROR   | TRANSACTION_FAILED  | FAILED         |
| Processor Error | 4000000000000341   | FAILURE | TRANSACTION_FAILED  | FAILED         |

## Understanding Result vs Error Code

The API response contains two independent fields for error scenarios:

- **`result`** -- The system-level outcome category. Tells you *what kind* of problem occurred.
- **`errorCode`** -- The user-facing error classification. Tells you *what to display or act on*.

These are derived independently, mirroring how real processors (e.g., Mastercard) map a single processor response code to both a result and an error code separately.

### Why do system errors all show TRANSACTION_FAILED?

For system/infrastructure errors (timeout, network error, processor error), the `errorCode` is intentionally normalized to the generic `TRANSACTION_FAILED`. The internal details (timeout vs network vs processor) are not exposed to API consumers. Instead, the **`result`** field differentiates them:

| Result    | Meaning                                                                 |
|-----------|-------------------------------------------------------------------------|
| `SUCCESS` | Transaction processed successfully                                      |
| `DECLINED`| Card/issuer declined the transaction (try a different card)             |
| `UNKNOWN` | Outcome is indeterminate -- the transaction may or may not have gone through (e.g., timeout) |
| `ERROR`   | A system error occurred on our side (e.g., network failure)             |
| `FAILURE` | The transaction definitively failed (e.g., processor rejected it)       |

This matches Mastercard's real processor behavior where `TIMED_OUT` maps to `ResultEnum::UNKNOWN` + `TransactionErrorEnum::TRANSACTION_FAILED`.

### Full Processing Pipeline

```
Card Number
  -> TestCardEnum::fromCardNumber()          -- look up the test card
  -> TestCardEnum::toErrorCode()             -- map to TransactionErrorEnum (null = success)
  -> SimulatedTransactionResponseFactory
     -> normalizeResultAndErrorCode()        -- derive result + errorCode independently
     -> createTransactionBody()              -- build the full response DTO
```

## Notes

- **Brand detection**: Known test cards use their predefined brand. Unknown card numbers are detected via BIN pattern (first digits: 4=Visa, 5=Mastercard, 34/37=Amex, 6011/65=Discover).
- **Card number formatting**: Spaces and dashes are stripped automatically, so `4242 4242 4242 4242` and `4242-4242-4242-4242` both work.
