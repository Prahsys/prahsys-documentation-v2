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
                                                                                                                                                                      
  ## How Testing Works                                                                                                                                                  
                                                                                                                                                                      
  In the CyberSource sandbox, transaction responses are controlled by the **payment amount** and **CVV value** you submit. Use any future expiration date (e.g., 01/30)
  and any billing address.                                     

  ## Simple Test Card Reference

  Use card number **4111 1111 1111 1111** (Visa) for all testing below.

  ### Transaction Responses

  | Card Number         | Amount  | CVV | Outcome                    |
  | ------------------- | ------- | --- | -------------------------- |
  | 4111 1111 1111 1111 | 1.00    | 001 | Approved                   |
  | 4111 1111 1111 1111 | -1      | 001 | Invalid Request            |
  | 4111 1111 1111 1111 | 7001.00 | 001 | Declined — Invalid Card    |
  | 4111 1111 1111 1111 | 7007.00 | 001 | Declined — Invalid Amount  |
  | 4111 1111 1111 1111 | 7012.00 | 001 | Declined — Invalid Expiry  |
  | 4111 1111 1111 1111 | 7120.00 | 001 | Declined — Invalid Currency |
  | 4111111111111112    | 1.00    | 001 | Declined — Failed Luhn Check |

  > Any normal amount (e.g., $1.00 - $999.00) will return an approved transaction. Amounts from $7,001.00 to $7,134.00 trigger specific decline codes. Amounts from
  $7,135.00 to $7,145.00 return approved.

  ### CVV / CVN Responses

  | Card Number         | Amount  | CVV | Outcome             |
  | ------------------- | ------- | --- | ------------------- |
  | 4111 1111 1111 1111 | 0.00    | 001 | CVV Match           |
  | 4111 1111 1111 1111 | 0.00    | 002 | CVV No Match        |
  | 4111 1111 1111 1111 | 0.00    | 003 | CVV Not Processed   |
  | 4111 1111 1111 1111 | 0.00    | 004 | CVV Suspicious      |
  | 4111 1111 1111 1111 | 0.00    | 005 | CVV Unavailable     |

  Or trigger CVV responses by amount (using any CVV like 123):

  | Card Number         | Amount  | CVV | Outcome             |
  | ------------------- | ------- | --- | ------------------- |
  | 4111 1111 1111 1111 | 2001.00 | 123 | CVV Match           |
  | 4111 1111 1111 1111 | 2002.00 | 123 | CVV No Match        |
  | 4111 1111 1111 1111 | 2003.00 | 123 | CVV Not Processed   |
  | 4111 1111 1111 1111 | 2004.00 | 123 | CVV Suspicious      |
  | 4111 1111 1111 1111 | 2005.00 | 123 | CVV Unavailable     |

  ### AVS (Address Verification) Responses

  Trigger AVS responses by amount (using any CVV like 123):

  | Card Number         | Amount  | CVV | Outcome                                  |
  | ------------------- | ------- | --- | ---------------------------------------- |
  | 4111 1111 1111 1111 | 1013.00 | 123 | AVS Full Match (address + zip)           |
  | 4111 1111 1111 1111 | 1001.00 | 123 | AVS Address Match Only (zip no match)    |
  | 4111 1111 1111 1111 | 1014.00 | 123 | AVS Zip Match Only (address no match)    |
  | 4111 1111 1111 1111 | 1009.00 | 123 | AVS No Match                             |
  | 4111 1111 1111 1111 | 1012.00 | 123 | AVS Unavailable                          |
  | 4111 1111 1111 1111 | 1011.00 | 123 | AVS System Unavailable (retry)           |
  | 4111 1111 1111 1111 | 1006.00 | 123 | AVS Not Supported (non-US issuer)        |

  Or trigger AVS responses by CVV value (using amount $0.00):

  | Card Number         | Amount | CVV | Outcome                                  |
  | ------------------- | ------ | --- | ---------------------------------------- |
  | 4111 1111 1111 1111 | 0.00   | 033 | AVS Full Match (address + zip)           |
  | 4111 1111 1111 1111 | 0.00   | 021 | AVS Address Match Only (zip no match)    |
  | 4111 1111 1111 1111 | 0.00   | 034 | AVS Zip Match Only (address no match)    |
  | 4111 1111 1111 1111 | 0.00   | 029 | AVS No Match                             |
  | 4111 1111 1111 1111 | 0.00   | 032 | AVS Unavailable                          |
  | 4111 1111 1111 1111 | 0.00   | 031 | AVS System Unavailable (retry)           |
  | 4111 1111 1111 1111 | 0.00   | 026 | AVS Not Supported (non-US issuer)        |

  ## Response Code Reference

  ### CVV Response Codes

  | Code | Meaning                                                              |
  | ---- | -------------------------------------------------------------------- |
  | M    | Match — CVV matched successfully                                     |
  | N    | No Match — CVV did not match                                         |
  | P    | Not Processed — CVV was not verified by the issuer                   |
  | S    | Suspicious — Merchant indicated CVV is present but issuer flagged it |
  | U    | Unavailable — Issuer does not support CVV verification               |

  ### AVS Response Codes

  | Code | Meaning                                              |
  | ---- | ---------------------------------------------------- |
  | Y    | Match: street address and postal code both match     |
  | A    | Partial: street address matches, postal code does not |
  | Z    | Partial: postal code matches, street address does not |
  | N    | No match: neither street address nor postal code match |
  | U    | Unavailable: address information not available        |
  | R    | Retry: system unavailable                             |
  | G    | Not supported: non-US issuer does not support AVS     |

  ## Test Card Numbers by Brand

  Use any of the cards below for sandbox testing. For most testing, **4111 1111 1111 1111** (Visa) is recommended.

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

  ## Important Notes

  - All test cards and simulation triggers **only work in the sandbox environment**.
  - Sandbox transactions are **never submitted to financial institutions** for processing.
  - Use any future expiration date (e.g., 01/30).
  - Where "Any" is listed for CVV, use any 3-digit number (or 4-digit for American Express).
  - **Do not combine** multiple trigger types (e.g., an AVS-triggering amount with a CVV-triggering CVV value) in a single transaction.