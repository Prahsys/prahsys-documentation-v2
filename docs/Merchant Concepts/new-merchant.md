---
title: New Merchant
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
        Details about the business.
      </Tab>

      <Tab title="owners">
        There should not more than 4 owners. Each owner must have at least 25% ownership.
      </Tab>

      <Tab title="controlProng">
        Information about the person with significant financial control over the business
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
      >      "isControllingProng": true, // [!code ++]
      >      "isPrimaryContact": true, // [!code ++]
      >      "isPciContact": true // [!code ++]
      >    }
      >   ],
      >  "bankAccount": { /** Properties */ },
      >  "controlProng": null, // [!code --]
      >  "primaryContact": null, // [!code --]
      >  "pciContact": null // [!code --]
      > }
      > ```

    </Column>
    
  </Columns>
</Callout>
