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

* Each test card number maps to a specific outcome
* Any unrecognized card number which passes LUHN validation is treated as a **success**
* Use any future expiration date, any CVV, and any billing address
* Amount and CVV values have no effect on the outcome

## Success Cards

Use any of these cards to get an approved transaction.

| Brand      | Card Number      | TestCardEnum |
| ---------- | ---------------- | ------------ |
| Visa       | 4242424242424242 | VISA         |
| Visa Debit | 4000056655665556 | VISA_DEBIT   |
| Mastercard | 5555555555554444 | MASTERCARD   |
| Amex       | 378282246310005  | AMEX         |
| Discover   | 6011111111111117 | DISCOVER     |

Any card number not listed on this page (success or error) is also treated as success. Brand is auto-detected from the BIN pattern.

## Error Cards

These cards simulate specific failure scenarios. Each card maps through a processing pipeline that produces a `result` and an `errorCode` in the API response.

### Decline Cards (card/issuer rejected the transaction)

| Scenario        | Card Number      | Result   | Error Code                  | Payment Status |
| --------------- | ---------------- | -------- | --------------------------- | -------------- |
| Generic Decline | 4000000000000002 | DECLINED | TRANSACTION_DECLINED        | DECLINED       |
| Card Expired    | 4000000000000069 | DECLINED | CARD_EXPIRED                | DECLINED       |
| Invalid Card    | 4000000000000127 | DECLINED | INVALID_CARD_NUMBER         | DECLINED       |
| CSC Mismatch    | 4000000000000101 | DECLINED | CSC_VERIFICATION_FAILED     | DECLINED       |
| AVS Mismatch    | 4000000000000010 | DECLINED | ADDRESS_VERIFICATION_FAILED | DECLINED       |

## Understanding Result vs Error Code

The API response contains two independent fields for error scenarios:

* **`result`** -- The system-level outcome category. Tells you _what kind_ of problem occurred.
* **`errorCode`** -- The user-facing error classification. Tells you _what to display or act on_.

These are derived independently, mirroring how real processors (e.g., Mastercard) map a single processor response code to both a result and an error code separately.

## Card Present Test Terminals (TIDs)

Card present transactions use **Terminal IDs (TIDs)** instead of card numbers to control transaction outcomes. Pass the TID as the `terminal` parameter in the pay request.

* Each TID maps to a specific outcome
* Any TID not in the list below returns `CARD_PRESENT_INVALID_TERMINAL`
* TID values are the error code names themselves, making test scenarios self-documenting

### Success Terminal

| TID       | Description                                                                   |
| --------- | ----------------------------------------------------------------------------- |
| `SUCCESS` | Approves all card present transactions successfully after internal validation |

### Decline Terminals (card/issuer rejected the transaction)

| Scenario           | TID                    | Result     | Error Code             | Payment Status |
| ------------------ | ---------------------- | ---------- | ---------------------- | -------------- |
| Generic Decline    | `TRANSACTION_DECLINED` | `DECLINED` | `TRANSACTION_DECLINED` | `DECLINED`     |
| Card Expired       | `CARD_EXPIRED`         | `DECLINED` | `CARD_EXPIRED`         | `DECLINED`     |
| Debit Requires PIN | `DEBIT_REQUIRES_PIN`   | `DECLINED` | `DEBIT_REQUIRES_PIN`   | `DECLINED`     |
| EULA Disagreed     | `EULA_DISAGREED`       | `DECLINED` | `EULA_DISAGREED`       | `DECLINED`     |

### Terminal Interaction Errors (device-level issues)

| Scenario              | TID                                     | Result    | Error Code                              | Payment Status |
| --------------------- | --------------------------------------- | --------- | --------------------------------------- | -------------- |
| Customer Aborted      | `CARD_PRESENT_ABORTED`                  | `FAILURE` | `CARD_PRESENT_ABORTED`                  | `ABORTED`      |
| Terminal Timeout      | `CARD_PRESENT_TIMEOUT`                  | `FAILURE` | `CARD_PRESENT_TIMEOUT`                  | `ABORTED`      |
| No Processor Response | `CARD_PRESENT_NO_RESPONSE`              | `FAILURE` | `CARD_PRESENT_NO_RESPONSE`              | `FAILED`       |
| Remove Card           | `CARD_PRESENT_REMOVE_CARD`              | `FAILURE` | `CARD_PRESENT_REMOVE_CARD`              | `FAILED`       |
| Duplicate Transaction | `CARD_PRESENT_DUPLICATE`                | `FAILURE` | `CARD_PRESENT_DUPLICATE`                | `FAILED`       |
| Cannot Connect        | `CARD_PRESENT_CANNOT_CONNECT_TO_DEVICE` | --        | `CARD_PRESENT_CANNOT_CONNECT_TO_DEVICE` | --             |

> **Cannot Connect** is special: the processor was never reached, so the transaction record is deleted entirely. No result or payment status is returned -- the pay request fails with an error response.
