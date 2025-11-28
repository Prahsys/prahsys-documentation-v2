---
title: DentTracks Merchant Onboarding Project
fullscreen: false
hidden: true
---
## Getting Started

Hey DentTracks Team, before you get started, I recommend you read up on our webhooks documentation to understand how we send webhooks and how to use webhooks in a developer environment for testing.

You will use webhooks to listen to the different status of a merchant and their progress during the merchant's underwriting process.

1. Read About Webhooks [Webooks](doc:webhooks)
2. Read about Ngrok tool [Local Testing with Ngrok](doc:webhooksngrok)

### Understand Merchant Entities

Because we have decided to do a custom onboarding, this will have a more extensive testing period. We have many different types of merchants that will try to sign up for payment processing. For each merchant, we require different pieces of information.

#### Understanding different merchants

We have different types of business entities that can apply for payment processing.

* **LIMITED (LLC)** A business structure that combines the liability protection of a corporation with the tax flexibility of a partnership, where owners are called "members."
* **CORPORATION** A separate legal entity owned by shareholders that provides maximum liability protection but is subject to corporate income tax in addition to personal taxes on dividends.
* **GOVERNMENT** A federal, state, local government agency or other governmental entity operating under public authority.
* **SOLE PROPRIETOR** An unincorporated business owned and operated by one individual where there is no legal distinction between the owner and the business entity.
* **PUBLIC COMPANY** A corporation whose ownership shares are traded on public stock exchanges and must comply with SEC reporting requirements.
* **NON PROFIT ORG** An organization incorporated to serve a charitable, educational, religious, or other public purpose where profits are reinvested rather than distributed to owners.
* **JOINT STOCK** A business entity where ownership is divided into transferable shares, similar to a corporation but typically with unlimited liability for shareholders.
* **PARTNERSHIP** A business owned by two or more individuals who share profits, losses, and management responsibilities without incorporating as a separate legal entity.

## Project Plan

I have broken up the project plan into separate plans. This first plan is all about merchant onboarding. We need to ensure to get this right in order to not run into any complications when payment processing. This is not hard, it's just detailed and requires different testing.

I've simplified the project plan to 4 milestones with each its own task.

<Cards columns={4}>
  <Card title="1. Create the form" icon="fa-id-card">
    You will need to create the custom merchant onboarding form. This will need the most testing because depending on the merchant entity type, we will require different fields.
  </Card>

  <Card title="2. Setup Webhook" icon="fa-arrow-up">
    Create your endpoint to listen to merchant status events. You will need to know if the merchant is denied or approved for payment processing.
  </Card>

  <Card title="3. Sign Agreement" icon="fa-check-square">
    To expedite the merchant onboarding process, I recommend we put the merchant agreement inside your application. It's an iframe docusign.
  </Card>

  <Card title="4. Add required UI" icon="fa-star">
    Inside your application, you need to provide visualization of the merchant status. We need to also add a button that allows the user to navigate to dashboard.prahsys.com.
  </Card>
</Cards>

## 1. Create the form

We need to create the form for merchant onboarding. It can look intimidating but we have many ways to simplify the process.

**Estimated Time**: 2-3 weeks

**Tasks**

* [ ] Create the form UI
* [ ] Hide fields when necessary
* [ ] Test a successful submission
  * [ ] `LIMITED`
  * [ ] `CORPORATION`
  * [ ] `GOVERNMENT`
  * [ ] `SOLE PROPRIETOR`
  * [ ] `PUBLIC COMPANY`
  * [ ] `NON PROFIT ORG`
  * [ ] `JOINT STOCK`
* [ ] Test a bad data submission
  * [ ] `LIMITED`
  * [ ] `CORPORATION`
  * [ ] `GOVERNMENT`
  * [ ] `SOLE PROPRIETOR`
  * [ ] `PUBLIC COMPANY`
  * [ ] `NON PROFIT ORG`
  * [ ] `JOINT STOCK`

<Accordion title="Example Merchant Body" icon="fa-info-circle">
  ```json
  {
    "legal": {
      "name": "Prahsys Test",
      "dba": "Ethan Prahsys test",
      "locationName": "Main Location",
      "taxId": "666989898",
      "address": {
        "street1": "500 S Main Street",
        "city": "Spokane",
        "state": "WA",
        "zipCode": "99206"
      },
      "mailingAddressSameAsBusinessAddress": true,
      "ownershipType": "CORPORATION",
      "category": "RETAIL",
      "productsSold": "Test Products",
      "phone": "+12234567890",
      "email": "business@example.com",
      "dateOfIncorporation": "2020-01-01T00:00:00.000Z",
      "website": "https://test.com",
      "averageTicketPrice": 100,
      "highTicketPrice": 500,
      "averageMonthlyVolume": 10000,
      "b2bTransactionPercentage": 60,
      "b2cTransactionPercentage": 40,
      "cardPresentPercentage": 60,
      "cardNotPresentPercentage": 40
    },
    "owners": [
      {
        "title": "CEO",
        "firstName": "John",
        "lastName": "Doe",
        "percentage": 100,
        "ssn": "666989898",
        "dob": "1990-05-05T00:00:00.000Z",
        "address": {
          "street1": "500 S Main Street",
          "city": "Spokane",
          "state": "WA",
          "zipCode": "99206"
        },
        "phone": "+12234567890",
        "email": "ethanbonin@gmail.com",
        "isControllingProng": true,
        "isPrimaryContact": true,
        "isPciContact": true
      }
    ],
    "bankAccount": {
      "name": "Chase",
      "routingNumber": "111000614",
      "confirmRoutingNumber": "111000614",
      "accountNumber": "987654321",
      "confirmAccountNumber": "987654321"
    }
  }
  ```
</Accordion>

We require 6 root keys when sending the merchant object.

1. `legal`
2. `owners`
3. `bankAccount`
4. `controlProng`
5. `primaryContact`
6. `piciContact`

### Required Fields for different Entities

✅ - Required  
❌ - Set as null  
🔸 - Required but you can _optionally_ mark one of the owners as the key

| Entity            | `legal` | `bankAccount` | `owners`     | `controlProng` | `primaryContact` | `pciContact` |
| :---------------- | :------ | :------------ | :----------- | :------------- | :--------------- | :----------- |
| `LIMITED`         | ✅       | ✅             | ✅            | 🔸             | 🔸               | 🔸           |
| `CORPORATION`     | ✅       | ✅             | ✅            | 🔸             | 🔸               | 🔸           |
| `PARTNERSHIP`     | ✅       | ✅             | ✅            | 🔸             | 🔸               | 🔸           |
| `JOINT STOCK`     | ✅       | ✅             | ✅            | 🔸             | 🔸               | 🔸           |
| `SOLE PROPRIETOR` | ✅       | ✅             | Only 1 owner | ❌              | 🔸               | 🔸           |
| `GOVERNMENT`      | ✅       | ✅             | ❌            | ❌              | ✅                | ✅            |
| `PUBLIC COMPANY`  | ✅       | ✅             | ❌            | ❌              | ✅                | ✅            |
| `NON PROFIT ORG`  | ✅       | ✅             | ❌            | ✅              | ✅                | ✅            |

### 🔸 Pro Tip

You do not need to provide the key in the root merchant object, if you mark one of the owners as the contact. For example, if the the `legal.ownershipType` is `LIMITED`, one of the owners could marked as the `controlProng`, `primaryContact` and `pciContact`, thus you would not need to provide this keys in the root object.

<Columns layout="auto">
  <Column>
    ```json BEFORE
    {
      "legal": {
        "ownershipType": "LIMITED"
      },
      "owners": [
        {
          "title": "CEO",
          "firstName": "John",
          "lastName": "Doe",
          // Magic here
          "isControllingProng": false,
          // Magic here
          "isPrimaryContact": false,
          // Magic here
          "isPciContact": false
        }
      ],
      "controlProng": { /* REQUIRED */ },
      "primaryContact": { /* REQUIRED */ },
      "pciContact": { /* REQUIRED */ },
      "bankAccount": { /* REQUIRED */ },
    }
    ```
  </Column>

  <Column>
    ```json AFTER
    {
      "legal": {
        "ownershipType": "LIMITED"
      },
      "owners": [
        {
          "title": "CEO",
          "firstName": "John",
          "lastName": "Doe",
          // Magic here
          "isControllingProng": true,
          // Magic here
          "isPrimaryContact": true,
          // Magic here
          "isPciContact": true
        }
      ],
      "bankAccount": { /* REQUIRED */ },
    }
    ```
  </Column>
</Columns>

#### New Mechant Zod Object

I recommend you just copy and paste our zod object for merchant onboarding. [New Merchant Zod Object](doc:new-merchant-zod-object)

## 2. Setup Webhook

**Estimated Time**: 2 hours

**Tasks**

* [ ] Create your endpoint on your backend to accept incoming webhook events from Prahsys
* [ ] Go to [Webhooks inside Prahsys Dashboard](https://dashboard.prahsys.com/denttracks/developers/webhooks) and add your endpoint
* [ ] Select the events you want to listen to for merchant status
* [ ] Setup authenticating webhooks inside your endpoint. [Authenticate Requests](https://docs.svix.com/receiving/verifying-payloads/how)
* [ ] Test sending webhook requests from Prahsys Dashboard

### Setup your webhook inside Prahsys Dashboard

<Image align="center" border={false} src="https://files.readme.io/128e6e2506246d3aec3d7c09af2b59a4ed0ece986187de7c0d0ee1964e500e6c-Screenshot_2025-11-28_at_12.04.30_PM.png" />

### Test sending your webhook from Prahsys Dashboard

<Image align="center" border={false} src="https://files.readme.io/6b6fa16fb366efe7c45b710a29c950c17cd504cab439616b5f839d54e93a3da0-Screenshot_2025-11-28_at_12.04.46_PM.png" />

## 3. Sign agreement

In order for the merchant to be approved for payment processing, the merchant must sign the merchant agreement. 

**Estimated Time**: 3 days

**Tasks**

* [ ] Listen for webhook event `APPLICATION_AWAITING_DIGITAL_SIGNATURE`
* [ ] Embed Docusign as an iFrame
* [ ] Have the merchant sign the agreement

<Callout icon="❗️">
  Sandbox merchants do not have to sign an agreement. You will need to submit a real merchant application to test this. Notify and Work with the Prahsys Team when you're ready to implement this. 
</Callout>

Please review the endpoint 
