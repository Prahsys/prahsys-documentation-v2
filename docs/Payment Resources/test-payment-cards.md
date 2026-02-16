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
                                                                                                                                                                        
  Test cards are special credit card numbers used to simulate transactions in the sandbox environment. They help you verify that your payment integration is working    
  correctly without using real money. Sandbox transactions are never submitted to financial institutions for processing.                                              
                                                                                                                                                                        
  Use these cards when testing your integrations. They will only work in sandbox mode.                                                                                  

  ## Test Card Numbers

  ### Quick Reference

  | Brand      | Card Number           | CVV |
  | ---------- | --------------------- | --- |
  | Visa       | 4111 1111 1111 1111   | Any |
  | Mastercard | 5555 5555 5555 4444   | Any |
  | Amex       | 3782 8224 6310 005    | Any |
  | Discover   | 6011 1111 1111 1117   | Any |

  ### Visa

  | Card Number              | CVV |
  | ------------------------ | --- |
  | 4111 1111 1111 1111      | Any |
  | 4622 9431 2701 3705      | 838 |
  | 4622 9431 2701 3713      | 043 |
  | 4622 9431 2701 3721      | 258 |
  | 4622 9431 2701 3739      | 942 |
  | 4622 9431 2701 3747      | 370 |

  ### Mastercard

  | Card Number              | CVV |
  | ------------------------ | --- |
  | 2222 4200 0000 1113      | Any |
  | 2222 6300 0000 1125      | Any |
  | 5555 5555 5555 4444      | Any |

  ### American Express

  | Card Number              | CVV  |
  | ------------------------ | ---- |
  | 3782 8224 6310 005       | Any  |

  ### Discover

  | Card Number              | CVV |
  | ------------------------ | --- |
  | 6011 1111 1111 1117      | Any |

  ### JCB

  | Card Number              | CVV |
  | ------------------------ | --- |
  | 3566 1111 1111 1113      | Any |

  ### Maestro (International)

  | Card Number                | CVV |
  | -------------------------- | --- |
  | 5033 9619 8909 17          | Any |
  | 5868 2416 0825 5333 38     | Any |

  ### Maestro (UK Domestic)

  | Card Number                | CVV |
  | -------------------------- | --- |
  | 6759 4111 0000 0008        | Any |
  | 6759 5600 4500 5727 054    | Any |
  | 5641 8211 1116 6669        | Any |

  ### UATP

  | Card Number              | CVV |
  | ------------------------ | --- |
  | 1354 1234 5678 911       | Any |

  ## Default Card Settings

  - **Expiration date:** Use any date in the future (e.g., 01/30)
  - **CVV:** Where a specific CVV is listed above, use that value. Otherwise, use any 3-digit number for Visa, Mastercard, Discover, JCB. Use any 4-digit number for
  American Express.

  ## Simulating Transaction Responses

  In the CyberSource sandbox, transaction responses are controlled by the **payment amount**, **CVV value**, or **invalid card data** you submit. This is how you
  simulate approvals, declines, errors, and other scenarios.

  ### Transaction Responses (Amount-Based)

  Use specific dollar amounts to trigger different transaction outcomes.

  | Amount       | Response        | Description                    |
  | ------------ | --------------- | ------------------------------ |
  | Any (e.g. 1) | AUTHORIZED      | Successful transaction         |
  | -1           | INVALID_REQUEST | Negative amount rejected       |
  | 100000000000 | INVALID_REQUEST | Amount exceeds maximum         |

  ### Simulating Declines with Invalid Card Data

  You can also trigger declines by submitting intentionally invalid card data.

  | Field            | Test Value         | Response        | Description               |
  | ---------------- | ------------------ | --------------- | ------------------------- |
  | Expiration Month | 13                 | INVALID_REQUEST | Invalid month              |
  | Expiration Year  | 1998               | DECLINE         | Expired card               |
  | Card Number      | 4111111111111112   | DECLINE         | Failed Luhn check          |
  | Card Number      | 42423482938483873  | DECLINE         | Invalid card number        |
  | Card Number      | 411111111111111111111 | DECLINE      | Card number too long (21 digits) |

  ### Common Reject Codes (Amount-Based)

  For more granular testing, these amounts trigger specific processor reject codes.

  | Amount  | Response Code | Description            |
  | ------- | ------------- | ---------------------- |
  | 7001.00 | 0001          | Invalid card number length |
  | 7002.00 | 0002          | Invalid card number    |
  | 7007.00 | 0009          | Invalid amount         |
  | 7012.00 | 0014          | Invalid expiration date |
  | 7054.00 | 0169          | Invalid name/location  |
  | 7059.00 | 0251          | Missing card number    |
  | 7067.00 | 0280          | Missing expiration date |
  | 7120.00 | 0730          | Invalid currency       |
  | 7135.00 | 00            | Approved               |
  | 7136.00 | 00            | Approved               |
  | 7137.00 | 00            | Approved               |
  | 7138.00 | 00            | Approved               |
  | 7139.00 | 00            | Approved               |
  | 7140.00 | 00            | Approved               |

  > Amounts from $7,001.00 to $7,134.00 trigger various decline and error responses. Amounts from $7,135.00 to $7,145.00 trigger successful approvals.

  ## CVV / CVN Responses

  You can simulate different CVV verification outcomes using two methods.

  ### Method 1: By CVV Value

  Submit the transaction with an amount of **$0.00** and one of these CVV values.

  | CVV | Response | Meaning       |
  | --- | -------- | ------------- |
  | 001 | M        | Match         |
  | 002 | N        | No Match      |
  | 003 | P        | Not Processed |
  | 004 | S        | Suspicious    |
  | 005 | U        | Unavailable   |

  ### Method 2: By Amount

  Submit the transaction with any CVV (e.g., 123) and one of these amounts.

  | Amount  | Response | Meaning       |
  | ------- | -------- | ------------- |
  | 2001.00 | M        | Match         |
  | 2002.00 | N        | No Match      |
  | 2003.00 | P        | Not Processed |
  | 2004.00 | S        | Suspicious    |
  | 2005.00 | U        | Unavailable   |

  ### CVV Response Code Definitions

  | Code | Meaning                                                                 |
  | ---- | ----------------------------------------------------------------------- |
  | M    | Match — CVV matched successfully                                        |
  | N    | No Match — CVV did not match                                            |
  | P    | Not Processed — CVV was not verified (issuer did not process)           |
  | S    | Suspicious — Merchant indicated CVV is present but issuer flagged it    |
  | U    | Unavailable — Issuer does not support CVV verification                  |

  ## AVS (Address Verification) Responses

  You can simulate different address verification outcomes using two methods.

  ### Method 1: By CVV Value

  Submit these CVV values to trigger specific AVS response codes.

  | CVV | AVS Code | Meaning                                       |
  | --- | -------- | --------------------------------------------- |
  | 021 | A        | Address matches, postal code does not          |
  | 022 | B        | Address matches, postal code not verified      |
  | 023 | I        | Address not verified                           |
  | 024 | D        | Address and postal code match (international)  |
  | 025 | M        | Address and postal code match (international)  |
  | 026 | G        | Issuer does not support AVS (non-US)           |
  | 027 | I        | Address not verified (international)           |
  | 028 | M        | Address and postal code match (international)  |
  | 029 | N        | No match (address and postal code)             |
  | 030 | P        | Postal code matches, address not verified      |
  | 031 | R        | System unavailable, retry                      |
  | 032 | U        | Address information unavailable                |
  | 033 | Y        | Address and postal code match                  |
  | 034 | Z        | Postal code matches, address does not          |

  ### Method 2: By Amount

  Submit these amounts to trigger specific AVS response codes.

  | Amount  | AVS Code | Meaning                                       |
  | ------- | -------- | --------------------------------------------- |
  | 1001.00 | A        | Address matches, postal code does not          |
  | 1002.00 | B        | Address matches, postal code not verified      |
  | 1003.00 | I        | Address not verified                           |
  | 1004.00 | D        | Address and postal code match (international)  |
  | 1005.00 | M        | Address and postal code match (international)  |
  | 1006.00 | G        | Issuer does not support AVS (non-US)           |
  | 1007.00 | I        | Address not verified (international)           |
  | 1008.00 | M        | Address and postal code match (international)  |
  | 1009.00 | N        | No match (address and postal code)             |
  | 1010.00 | P        | Postal code matches, address not verified      |
  | 1011.00 | R        | System unavailable, retry                      |
  | 1012.00 | U        | Address information unavailable                |
  | 1013.00 | Y        | Address and postal code match                  |
  | 1014.00 | Z        | Postal code matches, address does not          |

  ### AVS Response Code Definitions

  | Code | Meaning                                                    |
  | ---- | ---------------------------------------------------------- |
  | A    | Partial match: street address matches, postal code does not |
  | B    | Partial match: street address matches, postal code not verified |
  | D    | Match: address and postal code match (international)        |
  | G    | Not supported: issuer does not support AVS (non-US issuer)  |
  | I    | Not verified: address information not verified               |
  | M    | Match: address and postal code match (international)         |
  | N    | No match: neither street address nor postal code match       |
  | P    | Partial match: postal code matches, address not verified     |
  | R    | Retry: system unavailable                                    |
  | U    | Unavailable: address information not available               |
  | Y    | Match: street address and 5-digit postal code both match     |
  | Z    | Partial match: postal code matches, street address does not  |

  ## Important Notes

  - All test cards and simulation triggers **only work in the sandbox environment**.
  - Sandbox transactions are **never submitted to financial institutions** for processing.
  - The sandbox is **completely separate** from the live production environment and requires separate credentials.
  - When using amount-based triggers, the amount controls the response — do not combine multiple trigger types in a single transaction.

  This replaces your entire old doc. The key difference: CyberSource uses amounts and CVV values to control responses, not expiry dates and street addresses like your
  old gateway did.