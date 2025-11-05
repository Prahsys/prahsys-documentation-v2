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
  </Column>
</Columns>
