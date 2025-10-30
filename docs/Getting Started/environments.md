---
title: ' Environments'
deprecated: false
hidden: false
metadata:
  title: Environments | Prahsys Documentation
  description: >-
    Understand the difference between LIVE and SANDBOX environments in Prahsys
    API, including how to use test and production API keys, environment
    isolation, and common cross-environment access errors.
  keywords:
    - Prahsys environments
    - sandbox environment
    - live environment
    - test API keys
    - production API keys
    - sk_test
    - sk_live
    - environment isolation
    - API endpoints
    - cross-environment errors
  robots: index
---
We try to keep it simple. Prahsys has two environments: **LIVE** and **SANDBOX**.

* When you use your `sk_test_` key, you are in the **SANDBOX** environment, it will create sandbox data.
* When you use your `sk_live_` key, you are in the **LIVE** environment, it will create live (production) data.

> [!IMPORTANT]
>
> You cannot use your `sk_test_` key to pull live data, and you cannot use your `sk_live_` key to pull sandbox data. Each environment is isolated to ensure that your test data does not interfere with your production data.

| **Mode**    | **When to Use**                  | **What Happens**                                                                                                                                                        | **Key Prefix** |
| ----------- | -------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- |
| **Sandbox** | During development and staging   | Simulated data and responses; Everything will work as closely to production as possible. All response objects will be identical to what you will receive in production. | `sk_test_`     |
| **Live**    | With your production environment | Product related activities will occur. You will be charged for API interactions where applicable.                                                                       | `sk_live_`     |

## API Endpoints

Both the sandbox environment and the live environment use the same API endpoints. The only difference is the API key you use to authenticate your requests.

```curl
// Use your sk_test_ key for sandbox
// Use your sk_live_ key for live

https://api.prahsys.com
```

### Cross-Environment Access Errors

One of the most common mistakes is trying to access data from one environment using keys from another. Here's what happens:

#### Attempting to Access Live Data with Sandbox Key

_It will result in a 404_. This is because the sandbox key (`sk_test_`) is not authorized to access any live data, and the API will not return any results.

#### Attempting to Access Sandbox Data with Live Key

_It will result in a 404_. This is because the live key (`sk_live_`) is not authorized to access any sandbox data, and the API will not return any results.