---
title: DentTracks Merchant Onboarding Project
fullscreen: false
hidden: true
---
# Project Plan

<HTMLBlock>{`
<div style="position: relative; padding-bottom: 76.92307692307692%; height: 0;"><iframe src="https://www.loom.com/embed/98544dce00124389adfc541240633802" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe></div>
`}</HTMLBlock>

[New Merchant API Reference](ref:newmerchant)

**DentTracks Team**,
This project plan focuses exclusively on merchant onboarding—a critical foundation for successful payment processing integration. While the implementation is straightforward, it requires careful attention to detail and comprehensive testing across multiple merchant entity types.

The project is structured around **4 key milestones**, each with specific deliverables designed to ensure a smooth onboarding experience for your practices.

<Cards columns={4}>
  <Card title="1. Create the form" icon="fa-id-card">
    Build a dynamic onboarding form with entity-specific field validation. Requires comprehensive testing across all merchant types.
  </Card>

  <Card title="2. Setup Webhook" icon="fa-arrow-up">
    Configure webhook endpoints to receive merchant approval/denial events and update application status in real-time.
  </Card>

  <Card title="3. Sign Agreement" icon="fa-check-square">
    Integrate DocuSign iframe for seamless in-app merchant agreement signing, eliminating external redirects.
  </Card>

  <Card title="4. Add required UI" icon="fa-star">
    Display merchant status indicators and provide one-click access to the Prahsys Dashboard for ongoing merchant management.
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

### Understanding Merchant Entity Types

Custom merchant onboarding requires comprehensive testing across multiple business structures. Each entity type has distinct regulatory requirements and ownership disclosure rules that determine which fields your form must collect.

#### Supported Business Entities

Your integration must support eight different entity types, each with unique legal and tax characteristics:

**LIMITED (LLC)**  
A flexible business structure combining corporate liability protection with pass-through taxation. Owners are called "members" and can include individuals, corporations, or other LLCs.

**CORPORATION**  
A separate legal entity owned by shareholders. Provides strong liability protection but faces double taxation—once at the corporate level and again on shareholder dividends. Requires formal governance structure.

**PARTNERSHIP**  
An unincorporated business owned by two or more individuals who share profits, losses, and management duties. Partners typically have unlimited personal liability for business debts.

**SOLE PROPRIETOR**  
The simplest business structure where one individual owns and operates the business. No legal separation exists between owner and business—all assets and liabilities belong to the individual.

**GOVERNMENT**  
Federal, state, or local government agencies operating under public authority. Subject to different regulatory requirements than private entities.

**PUBLIC COMPANY**  
A corporation with shares traded on public stock exchanges (NYSE, NASDAQ). Must comply with SEC reporting requirements and generally does not disclose individual ownership in merchant applications.

**NON PROFIT ORG**  
A tax-exempt organization serving charitable, educational, religious, or public purposes. Profits must be reinvested into the mission rather than distributed to owners or members.

**JOINT STOCK**  
A hybrid entity with transferable ownership shares like a corporation, but shareholders typically retain unlimited personal liability. Less common in modern business but still legally recognized.

#### Why Entity Type Matters

Each entity type has different requirements for:

* **Ownership disclosure** (some require individual owners, others do not)
* **Control person identification** (who has authority over the business)
* **Tax documentation** (EIN vs SSN, tax-exempt status)
* **Liability structure** (limited vs unlimited)

Your form's conditional logic must adapt to these requirements to ensure compliant merchant applications.

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

### Merchant Object Structure

The merchant onboarding payload consists of six root-level keys. Understanding when each key is required—and when it can be omitted or designated from existing owner data—is critical for proper form implementation.

#### Root-Level Keys

1. **`legal`** - Business information (name, address, tax ID, industry details)
2. **`owners`** - Array of individuals with 25%+ ownership or control
3. **`bankAccount`** - Bank account details for settlement
4. **`controlProng`** - Individual with significant control over the business
5. **`primaryContact`** - Main point of contact for business operations
6. **`pciContact`** - Contact for PCI compliance and security matters

### Field Requirements by Entity Type

The table below shows which fields are required for each business entity. Pay special attention to the 🔸 symbol—these fields can either be provided as separate root-level objects OR designated from the `owners` array.

**Legend:**

* ✅ **Required** - Must be provided
* ❌ **Not Required** - Must be null or omitted
* 🔸 **Flexible** - Required, but can be designated from `owners` array instead of separate object

| Entity            | `legal` | `bankAccount` | `owners`      | `controlProng` | `primaryContact` | `pciContact` |
| :---------------- | :------ | :------------ | :------------ | :------------- | :--------------- | :----------- |
| `LIMITED`         | ✅       | ✅             | ✅             | 🔸             | 🔸               | 🔸           |
| `CORPORATION`     | ✅       | ✅             | ✅             | 🔸             | 🔸               | 🔸           |
| `PARTNERSHIP`     | ✅       | ✅             | ✅             | 🔸             | 🔸               | 🔸           |
| `JOINT STOCK`     | ✅       | ✅             | ✅             | 🔸             | 🔸               | 🔸           |
| `SOLE PROPRIETOR` | ✅       | ✅             | ✅ (exactly 1) | ❌              | 🔸               | 🔸           |
| `GOVERNMENT`      | ✅       | ✅             | ❌             | ❌              | ✅                | ✅            |
| `PUBLIC COMPANY`  | ✅       | ✅             | ❌             | ❌              | ✅                | ✅            |
| `NON PROFIT ORG`  | ✅       | ✅             | ❌             | ✅              | ✅                | ✅            |

#### Key Insights

**Entities with Individual Ownership** (LIMITED, CORPORATION, PARTNERSHIP, JOINT STOCK, SOLE PROPRIETOR)

* Must provide `owners` array with individuals owning 25%+ or having significant control
* Can simplify the payload by marking one owner as `controlProng`, `primaryContact`, and/or `pciContact`
* If marked on an owner, those keys don't need to be provided at root level

**Entities without Individual Ownership** (GOVERNMENT, PUBLIC COMPANY, NON PROFIT ORG)

* Do NOT provide `owners` array
* Must provide separate `primaryContact` and `pciContact` objects at root level
* `controlProng` requirements vary (not needed for GOVERNMENT/PUBLIC COMPANY, required for NON PROFIT ORG)

**Special Case: SOLE PROPRIETOR**

* Must have exactly ONE owner (the individual proprietor)
* `controlProng` is NOT required and should be omitted (the single owner is implicitly the control person)
* Can mark the owner as `primaryContact` and `pciContact` to simplify the payload

### 🔸 Simplifying Your Payload with Owner Designation

For entity types with the 🔸 flexible designation option, you can significantly reduce payload complexity by marking an owner to fulfill contact roles. This eliminates the need for separate root-level contact objects.

**How it works:** Set `isControllingProng`, `isPrimaryContact`, and/or `isPciContact` to `true` on one of your owners. When these flags are enabled, you can omit the corresponding root-level keys entirely.

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

#### Validation Schema

We strongly recommend using our pre-built Zod validation schema for merchant onboarding. This schema includes all entity-specific validation rules, conditional field requirements, and data format constraints to ensure your submissions match API expectations exactly.

**[View the complete New Merchant Zod Object →](doc:new-merchant-zod-object)**

Copy the Zod schema directly into your codebase for:

* **Client-side validation** before API submission
* **Type safety** in TypeScript projects
* **Guaranteed compatibility** with Prahsys API requirements
* **Reduced debugging** by catching errors before submission

The schema handles all conditional logic for entity types, ownership percentage validation, required field enforcement, and data format rules (phone numbers, dates, SSN/TaxID, etc.).

## 2. Setup Webhook

Webhooks provide real-time notifications about merchant application status changes. Your application needs to receive these events to update the merchant's onboarding progress and inform users when they're approved, denied, or need to take action.

**Estimated Time**: 2 hours

### Tasks

* [ ] **Create webhook endpoint on your backend**
  * Build a POST endpoint to receive webhook events from Prahsys
  * Implement request parsing and event handling logic
  * Add error handling and logging for debugging

* [ ] **Register endpoint in Prahsys Dashboard**
  * Navigate to [Webhooks in Prahsys Dashboard](https://dashboard.prahsys.com/denttracks/developers/webhooks)
  * Add your webhook endpoint URL
  * Save and verify the endpoint is active

* [ ] **Subscribe to merchant status events**
  * Select relevant merchant application events (e.g., `APPLICATION_SUBMITTED`, `APPLICATION_APPROVED`, `APPLICATION_DENIED`, `APPLICATION_AWAITING_DIGITAL_SIGNATURE`)
  * Configure event filters if needed
  * Ensure critical events are not missed

* [ ] **Implement webhook signature verification**
  * Follow [Svix authentication guide](https://docs.svix.com/receiving/verifying-payloads/how) to verify webhook authenticity
  * Reject webhooks with invalid signatures to prevent spoofing
  * Add signature validation before processing any webhook data

* [ ] **Test webhook delivery**
  * Use the Prahsys Dashboard to send test webhook events
  * Verify your endpoint receives and processes events correctly
  * Check logs for any errors or missing data

### Understanding Webhooks

Webhooks are HTTP callbacks that notify your application when merchant status changes occur. During the underwriting process, merchants progress through multiple states—your application must listen for these events to provide accurate status updates to users.

**Essential Reading:**

1. **[Webhooks Documentation](doc:webhooks)** - Learn how Prahsys sends webhooks, payload structure, event types, and retry logic
2. **[Local Testing with Ngrok](doc:webhooksngrok)** - Set up Ngrok to receive webhooks on your local development environment

### Setup your webhook inside Prahsys Dashboard

<Image align="center" border={false} src="https://files.readme.io/128e6e2506246d3aec3d7c09af2b59a4ed0ece986187de7c0d0ee1964e500e6c-Screenshot_2025-11-28_at_12.04.30_PM.png" />

### Test sending your webhook from Prahsys Dashboard

<Image align="center" border={false} src="https://files.readme.io/6b6fa16fb366efe7c45b710a29c950c17cd504cab439616b5f839d54e93a3da0-Screenshot_2025-11-28_at_12.04.46_PM.png" />

## 3. Sign Agreement

Merchant agreement signing is a required step in the underwriting process. Once a merchant application passes initial review, they must electronically sign the merchant processing agreement before final approval. Embedding this signature flow directly in your application creates a seamless onboarding experience.

**Estimated Time**: 3 days

### Tasks

* [ ] **Listen for signature webhook event**
  * Monitor for `APPLICATION_AWAITING_DIGITAL_SIGNATURE` webhook
  * Update merchant status in your UI when event is received
  * Trigger the agreement signing workflow

* [ ] **Generate DocuSign URL**
  * Call the [Generate Application DocuSign URL](ref:generateapplicationdocusignurl) endpoint
  * Pass the merchant application ID
  * Receive back the DocuSign embedded signing URL

* [ ] **Embed DocuSign iframe**
  * Display the DocuSign signing interface within your application
  * Use an iframe to keep the user in your UI
  * Handle iframe loading states and errors gracefully

* [ ] **Handle signing completion**
  * Listen for webhook events indicating signature completion
  * Update merchant status to reflect signed agreement
  * Show success message and next steps to the user

* [ ] **Handle signing abandonment**
  * Provide ability for merchant to return and complete signing later
  * Store the DocuSign URL for retrieval if user navigates away
  * Send reminder notifications if signature is pending for extended period

### Implementation Flow

1. **Receive webhook** → `APPLICATION_AWAITING_DIGITAL_SIGNATURE`
2. **Update UI** → Show "Agreement Ready for Signature" status
3. **User clicks "Sign Agreement"** → Call Prahsys API to generate DocuSign URL
4. **Display iframe** → Embed DocuSign signing interface
5. **User signs** → DocuSign processes signature
6. **Receive webhook** → Agreement signed successfully
7. **Update UI** → Show "Application Under Final Review" status

### Important Notes

<Callout icon="❗️" theme="error">
  **Sandbox Testing Limitation**

  Sandbox merchants do NOT require agreement signing—applications are auto-approved without this step. To test the complete signing workflow, you must:

  * Submit a **real merchant application** (not sandbox)
  * Coordinate with the **Prahsys team** before implementation
  * Use production credentials (not sandbox API keys)

  Contact Prahsys via Slack when you're ready to test agreement signing in a controlled production environment.
</Callout>

### API Reference

Review the complete endpoint documentation to understand request parameters, response structure, and error handling:

**[Generate Application DocuSign URL](ref:generateapplicationdocusignurl)**

### Best Practices

**User Experience:**

* Clearly communicate that signing is required before approval
* Show progress indicator during DocuSign URL generation
* Provide "Save and Continue Later" option for merchants who need time to review
* Display estimated time to complete (typically 2-3 minutes)

**Error Handling:**

* Handle cases where DocuSign URL generation fails
* Provide clear error messages if signing session expires
* Allow merchants to regenerate signing URL if needed

**Security:**

* DocuSign URLs are single-use and time-limited
* Never cache or store DocuSign URLs for extended periods
* Verify webhook signatures to confirm authentic signing events

<br />

## 4. Add Required UI

**Estimated Time**: 1 day

**Tasks**

* [ ] Add button for merchant to be able to get the Prahsys Dashboard.
  * [ ] Goes to link: [https://dashboard.prahsys.com/](https://dashboard.prahsys.com/)
* [ ] Add Merchant Status Badge
  * [ ] User needs to know the status of their merchant application. Provide a visual indicator where they are in the steps.

## Merchant Application Flow

<Image align="center" border={false} caption="Merchant onboarding and underwriting experience" src="https://files.readme.io/a1c14ec5fdbdbfb605623bec3c605324e7ee07a351962a660e4fa18baaf7003a-Merchant_Onboarding_Diagram.drawio.png" />

<br />
