---
title: API Keys
excerpt: >-
  Prahsys authenticates your API requests using your account's API keys. If a
  request doesn't include a valid key, Prahsys returns an invalid request 401
  error.  ##
deprecated: false
hidden: false
metadata:
  title: API Keys | Prahsys Documentation
  description: >
    Learn how to authenticate API requests with Prahsys API keys, including
    sandbox and live environments, implementation best practices, and security
    guidelines.
  keywords:
    - Prahsys API keys
    - API authentication
    - sandbox environment
    - test API keys
    - live API keys
    - API key security
    - key rotation
    - API troubleshooting
    - bearer token authentication
  robots: index
---
## Sandbox Environment

The Sandbox environment is a separate, isolated instance of the Prahsys platform dedicated exclusively to testing and development. Key features of the Sandbox include:

| Feature                 | Description                                                                                                                                                                                              |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Isolated Data**       | Organizations/Merchants/Users created in Sandbox mode exist only in the testing environment and are completely separate from the production database                                                     |
| **Test-Only Keys**      | Only test API keys (beginning with `sk_test_`) can be used with Sandbox merchants                                                                                                                        |
| **Full API Access**     | All API endpoints and features available in production are also available in the Sandbox                                                                                                                 |
| **Simulated Responses** | Our API will simulate as much as possible for your testing. All data is isolated from your production data, and no real world operations will be performed where applicable (such as payment processing) |

### Using the Sandbox Environment

When integrating with the Sandbox:

* Use the same API endpoints as production, but with your test API keys
* All operations are simulated (no real money movement occurs)
* Test all error cases and edge scenarios
* Verify webhooks and notifications
* Test your integration thoroughly before moving to production

### Sandbox vs Live Mode

All Prahsys API requests occur in either sandbox or live mode. Each mode has its own set of API keys, and objects in one mode aren't accessible to the other.

All API Keys are formatted: `sk_[ENVIRONMENT]_[RANDOM_HASH]`

| **Mode**    | **When to Use**                  | **What Happens**                                                                                                                                                                | **Key Prefix** |
| ----------- | -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- |
| **Sandbox** | During development and staging   | Simulated transactions and responses; Everything will work as closely to production as possible. All response objects will be identical to what you will receive in production. | `sk_test_`     |
| **Live**    | With your production environment | Product related activities will occur. You will be charged for API interactions where applicable.                                                                               | `sk_live_`     |

> **Important**: You can only reveal a live mode secret key once. If you lose it, you'll need to create a new one.

## Moving from Sandbox to Live (Production)

1. Complete all testing in the Sandbox environment
2. Swap out your key `sk_test_...` for your live key `sk_live_...`

> **Important Note**: Test API keys (`sk_test_...`) can be used with both Sandbox merchant accounts and live merchant accounts. When test keys are used with live merchant accounts, transactions are still simulated and no real money is moved, but the test data will appear alongside your live data.

## Using API Keys

### Authentication Headers

Include your API key in the `Authorization` header of all API requests:

```bash {{title: "HTTP Headers"}}
Authorization: Bearer sk_[ENVIRONMENT]_[SECRET_SAUCE]
```

> **Important Note:** Sandbox API keys (**sk_test_XXX**) can be used with both Sandbox accounts and live accounts.
> When test keys are used with live accounts, operations are still simulated, but the test data will appear alongside your live data.

## API Key Management

Manage your API keys through the Prahsys Dashboard:

1. Navigate to **Dashboard** > **Developers** > **API Keys**
2. View existing keys
3. Generate new keys
4. Delete existing keys

### Security Best Practices

* **Never share your secret keys** or include them in client-side code
* **Store keys in environment variables** or secure key management systems
* **Use separate keys for different applications** to limit breach impact
* **Implement key rotation** as part of your security procedures
* **Use sandbox keys exclusively for testing** to avoid accidental live transactions

## Implementation Examples

```bash title="Get API status" /$PRAHSYS_API_KEY/
curl -X GET https://api.prahsys.com/merchant/status \
-H "Authorization: Bearer $PRAHSYS_API_KEY" \
-H "Content-Type: application/json"
```

## Key Rotation

If you suspect a key has been compromised, or as part of regular security maintenance:

1. Generate a new API key in the Prahsys Merchant Dashboard
2. Update your applications to use the new key
3. Verify functionality with the new key
4. Delete the old key

## Troubleshooting

| Error              | Possible Cause                                  | Solution                                                             |
| ------------------ | ----------------------------------------------- | -------------------------------------------------------------------- |
| `401 Unauthorized` | Invalid or expired API key                      | Verify you're using the correct API key                              |
| `403 Forbidden`    | Insufficient permissions                        | Contact your account manager to adjust permissions                   |
| `404 Not Found`    | Attempting to access object from different mode | Ensure you're using matching sandbox or live keys for all operations |

For additional assistance, contact [Prahsys Support](\{routes.support\(\)}).