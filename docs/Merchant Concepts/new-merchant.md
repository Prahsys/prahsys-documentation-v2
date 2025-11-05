---
title: Create a New Merchant
excerpt: >-
  We've simplified the merchant creation process with an intuitive request
  structure. The new merchant object is organized into three straightforward
  sections: legal, owners, and bankAccount
deprecated: false
hidden: false
icon: far fa-house-chimney-user
metadata:
  title: Creating a New Merchant | Prahsys Documentation
  description: >-
    Complete reference guide for creating new merchants with Prahsys API,
    including legal information, owners details, and bank account validation
    requirements.
  keywords:
    - Prahsys NewMerchant object
    - merchant onboarding API
    - create merchant account
    - merchant validation schema
    - merchant legal information
    - business owner validation
    - bank account validation
    - payment processing onboarding
    - merchant data structure
  robots: index
---
<Columns layout="auto">
  <Column>
    <Tabs>
      <Tab title="legal">
        We collect essential legal information (business name, address, and tax ID) to verify your business legitimacy and establish a unique merchant identifier. This is the details about the business.
      </Tab>

      <Tab title="owners">
        There should not more than 4 owners. Each owner must have at least 25% ownership. In order to create a unique identifier for the merchant, we need to know who owns this business. The owner object is an array of objects. Each object represents an owner of the business.

        Sometimes the owner array is not needed depending on the type of business. Read more about ownershipType and how they affect the owner array.
      </Tab>

      <Tab title="controlProng">
        Information about the person with significant financial control over the business
      </Tab>

      <Tab title="primaryContact">
        Information about the primary business contact person
      </Tab>

      <Tab title="pciContact">
        Contact information for the person responsible for PCI compliance
      </Tab>

      <Tab title="bankAccount">
        Bank account information for the merchant
      </Tab>
    </Tabs>

    <Column>
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
    </Column>
  </Column>
</Columns>

<Callout icon="📘" theme="info">
  <Columns layout="auto">
    <Column>
      When you specfic one of the owners as the **control prong**, **pci contact** or **primary Contact**, you do not have to provide the properties of `controlProng`, `pciContact` or `primaryContact`.
    </Column>

    <Column>
      > ```json
      > {
      >  "legal": {
      >    // ... Other Properties
      >    "ownershipType": "LIMITED"
      >  },
      >  "owners": [
      >    {
      >      "title": "CEO",
      >      "firstName": "John",
      >      "lastName": "Doe",
      >      "percentage": 100,
      >      "ssn": "666989898",
      >      // Set as true to make controlProng null
      >      "isControllingProng": true, 
      >      // Set as true to make isPrimaryContact null
      >      "isPrimaryContact": true,
      >      // Set as true to make isPciContact null
      >      "isPciContact": true
      >    }
      >   ],
      >  "bankAccount": { /** Properties */ },
      >  // set as null since its not needed
      >  "controlProng": null, 
      >  // set as null since its not needed
      >  "primaryContact": null,
      >  // set as null since its not needed
      >  "pciContact": null
      > }
      > ```
    </Column>
  </Columns>
</Callout>

<br />

### Ownership Type Requirements

#### `legal.ownershipType`

The ownershipType tells us how the business is structured.
Read the ownershipTypes below to understand what fields are required for each type of business.

<Columns>
  <Column>
    #### `LIMITED`

    This is a limited liability company (LLC). When ownershipType is `LIMITED`, there should be **owners** and a **control prong**.
  </Column>

  <Column>
    ```json LIMITED
    {
        "legal": {
          "ownershipType": "LIMITED",
        },
        "bankAccount": { /** Properties */ },
// Owners Required
        "owners": [ {} ], 
// ControlProng Required
        "controlProng": {} 
    }
    ```
  </Column>
</Columns>

<Columns>
  <Column>
    #### `CORPORATION`

    This is a legally incorporated entity separate from its owners. When ownershipType is `CORPORATION`, there should be **owners** and a **control prong**.
  </Column>

  <Column>
    ```json CORPORATION
    {
        "legal": {
          "ownershipType": "CORPORATION",
        },
        "bankAccount": { /** Properties */ },
        "owners": [ {} ], // [!code ++]
        "controlProng": {} // [!code ++]
    }
    ```
  </Column>
</Columns>

<Columns>
  <Column>
    #### `GOVERNMENT`

    This is a government entity or agency. When ownershipType is `GOVERNMENT`, **no owners or control prong** should be provided.
    You must also specify the **primary contact** and **PCI contact**.
  </Column>

  <Column>
    ```json GOVERNMENT
    {
        "legal": {
          "ownershipType": "GOVERNMENT",
        },
        "bankAccount": { /** Properties */ },
        "owners": null, // [!code --]
        "controlProng": null, // [!code --]
        "primaryContact": {}, // [!code ++]
        "pciContact": {} // [!code ++]
    }
    ```
  </Column>
</Columns>

<Columns>
  <Column>
    #### `SOLE PROPRIETOR`

    This is a business owned and operated by a single individual. When ownershipType is `SOLE PROPRIETOR`, there should be **only 1 owner** and **no control prong**.

    The `legal.name` field is required and should be the same as the owner's name.
  </Column>

  <Column>
    ```json SOLE PROPRIETOR
    {
        "legal": {
          "name": "John Doe", // [!code ++]
          "ownershipType": "SOLE PROPRIETOR",
        },
        "bankAccount": { /** Properties */ },
        "owners": [
            // Only 1 owner should exist for SOLE PROPRIETOR
            { "firstName": "John", "lastName": "Doe", } // [!code ++]
        ],
        "controlProng": null // [!code --]
    }
    ```
  </Column>
</Columns>

<Columns>
  <Column>
    #### `PUBLIC COMPANY`

    This is a corporation that offers securities for public trading. When ownershipType is `PUBLIC COMPANY`, **no owners or control prong** should be provided.
    You must also specify the **primary contact** and **PCI contact**.
  </Column>

  <Column>
    ```json PUBLIC COMPANY
    {
        "legal": {
          "ownershipType": "PUBLIC COMPANY",
        },
        "bankAccount": { /** Properties */ },
        "owners": null, // [!code --]
        "controlProng": null, // [!code --]
        "primaryContact": {}, // [!code ++]
        "pciContact": {} // [!code ++]
    }
    ```
  </Column>
</Columns>

<Columns>
  <Column>
    #### `NON PROFIT ORG`

    This is a non-profit organization with tax-exempt status. When ownershipType is `NON PROFIT ORG`, there should be **no owners**. There should be a **control prong**.
    You must also specify the **primary contact** and **PCI contact**.
  </Column>

  <Column>
    ```json NON PROFIT ORG
    {
        "legal": {
          "ownershipType": "NON PROFIT ORG",
        },
        "bankAccount": { /** Properties */ },
        "owners": null, // [!code --]
        "controlProng": {}, // [!code ++]
        "primaryContact": {}, // [!code ++]
        "pciContact": {} // [!code ++]
    }
    ```
  </Column>
</Columns>

<Columns>
  <Column>
    #### `JOINT STOCK`

    This is a joint-stock company with shared capital ownership. When ownershipType is `JOINT STOCK`, there should be **owners** and a **control prong**.
  </Column>

  <Column>
    ```json JOINT STOCK
    {
        "legal": {
          "ownershipType": "JOINT STOCK",
        },
        "bankAccount": { /** Properties */ },
        "owners": [ {} ], // [!code ++]
        "controlProng": {} // [!code ++]
    }
    ```
  </Column>
</Columns>

<Columns>
  <Column>
    #### `PARTNERSHIP`

    This is a business owned by two or more partners. When ownershipType is `PARTNERSHIP`, there should be **owners** and a **control prong**.
  </Column>

  <Column>
    ```json PARTNERSHIP
    {
        "legal": {
          "ownershipType": "PARTNERSHIP",
        },
        "bankAccount": { /** Properties */ },
        "owners": [ {} ], // [!code ++]
        "controlProng": {} // [!code ++]
    }
    ```
  </Column>
</Columns>
