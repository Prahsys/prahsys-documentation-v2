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
## What Are Test Cards?

Test cards are special credit card numbers used to simulate transactions in a testing environment. They help you see how a payment gateway (the system handling online payments) responds to various conditions without using real money.

Uses these cards when testing your integrations to simulate real-world transaction responses.

## Transaction Response Explanations

The following terms correspond to the Transaction Response tables you'll see below, here is what these response values are trying to communicate:

| Term                  | Definition                                  |
| --------------------- | ------------------------------------------- |
| APPROVED              | The transaction went through.               |
| DECLINED              | The transaction was rejected.               |
| EXPIRED_CARD          | The card has expired.                       |
| TIMED_OUT             | The transaction took too long.              |
| ACQUIRER_SYSTEM_ERROR | There was an error with the payment system. |

# Simple Test Cards Reference

## Visa Cards

| CARD NUMBER      | EXPIRY DATE | CVV | ADDRESS  | OUTCOME      |
| ---------------- | ----------- | --- | -------- | ------------ |
| 4508750015741019 | 01/39       | 100 | Alpha St | Approved     |
| 4508750015741019 | 05/39       | 100 | Alpha St | Declined     |
| 4508750015741019 | 04/27       | 100 | Alpha St | Expired Card |
| 4508750015741019 | 08/28       | 100 | Alpha St | Timed Out    |
| 4508750015741019 | 01/39       | 102 | Alpha St | CVV No Match |

## Mastercard

| CARD NUMBER      | EXPIRY DATE | CVV | ADDRESS     | OUTCOME          |
| ---------------- | ----------- | --- | ----------- | ---------------- |
| 5123450000000008 | 01/39       | 100 | Alpha St    | Approved         |
| 5123450000000008 | 05/39       | 100 | Alpha St    | Declined         |
| 5123450000000008 | 01/39       | 102 | Alpha St    | CVV No Match     |
| 5123450000000008 | 01/39       | 100 | November St | Address No Match |

## American Express

| CARD NUMBER     | EXPIRY DATE | CVV  | ADDRESS     | OUTCOME          |
| --------------- | ----------- | ---- | ----------- | ---------------- |
| 345678901234564 | 01/39       | 0000 | Alpha St    | Approved         |
| 345678901234564 | 04/37       | 0000 | Alpha St    | Declined         |
| 345678901234564 | 05/18       | 0000 | Alpha St    | Expired Card     |
| 345678901234564 | 01/39       | 1111 | Alpha St    | CVV No Match     |
| 345678901234564 | 01/39       | 0000 | November St | Address No Match |

## Discover

| CARD NUMBER      | EXPIRY DATE | CVV | ADDRESS  | OUTCOME  |
| ---------------- | ----------- | --- | -------- | -------- |
| 6011003179988686 | 01/39       | 100 | Alpha St | Approved |
| 6011003179988686 | 05/39       | 100 | Alpha St | Declined |

## Standard test cards (Card Network) All supported regions

| Test Cards       | Card Number      |
| ---------------- | ---------------- |
| Mastercard       | 5123450000000008 |
| Visa             | 4508750015741019 |
| American Express | 345678901234564  |
| Discover         | 6011003179988686 |

## Transaction responses

| Transaction Response Gateway Code | Expiry Date |
| --------------------------------- | ----------- |
| APPROVED                          | 01/39       |
| DECLINED                          | 05/39       |
| EXPIRED_CARD                      | 04/27       |
| TIMED_OUT                         | 08/28       |
| ACQUIRER_SYSTEM_ERROR             | 01/37       |
| UNSPECIFIED_FAILURE               | 02/37       |
| UNKNOWN                           | 05/37       |

## CSC/CVV responses

| CSC/CVV Response Gateway Code         | CSC/CVV |
| ------------------------------------- | ------- |
| MATCH                                 | 100     |
| NOT_PROCESSED                         | 101     |
| NO_MATCH                              | 102     |
| (American Express ONLY) MATCH         | 1000    |
| (American Express ONLY) NOT_PROCESSED | 1010    |
| (American Express ONLY) NO_MATCH      | 1020    |

## AVS responses

| AVS Response Gateway Code   | Billing Address Street |
| --------------------------- | ---------------------- |
| ADDRESS_MATCH               | Alpha St               |
| NO_MATCH                    | November St            |
| SERVICE_NOT_AVAILABLE_RETRY | Romeo St               |
| SERVICE_NOT_SUPPORTED       | Sierra St              |
| NOT_AVAILABLE               | Uniform St             |
| ZIP_MATCH                   | Whiskey St             |
| ADDRESS_ZIP_MATCH           | X-ray St               |

## American Express test cards (Card Network & Acquirer) All supported regions

| Test Card        | Card Number     |
| ---------------- | --------------- |
| American Express | 345678901234564 |

## American Express Transaction responses

| Transaction Response Gateway Code | Expiry Date |
| --------------------------------- | ----------- |
| APPROVED                          | 01/39       |
| UNSPECIFIED_FAILURE               | 04/19       |
| DECLINED                          | 04/37       |
| EXPIRED_CARD                      | 05/18       |
| ACQUIRER_SYSTEM_ERROR             | 11/18       |

## American Express CSC/CVV responses

| CSC/CVV Response Gateway Code | CSC/CVV |
| ----------------------------- | ------- |
| MATCH                         | 0000    |
| NO_MATCH                      | 1111    |
| NOT_PROCESSED                 | 2222    |

## American Express AVS responses

| AVS Response Gateway Code   | Billing Address Street |
| --------------------------- | ---------------------- |
| ADDRESS_MATCH               | Alpha St               |
| ADDRESS_ZIP_MATCH           | Mike St                |
| NOT_AVAILABLE               | Uniform St             |
| NAME_MATCH                  | Kilo St                |
| NO_MATCH                    | November St            |
| ZIP_MATCH                   | Zulu St                |
| SERVICE_NOT_AVAILABLE_RETRY | Romeo St               |
| SERVICE_NOT_SUPPORTED       | Sierra St              |
| NAME_ZIP_MATCH              | Love St                |
| NAME_ADDRESS_MATCH          | Olive St               |
| NOT_REQUESTED               | Zero St                |
