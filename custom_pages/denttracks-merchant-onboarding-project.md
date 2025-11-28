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

### First Steps

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
  <Card title="1. Creating the form" icon="fa-id-card">
    You will need to create the custom merchant onboarding form. This will need the most testing because depending on the merchant entity type, we will require different fields.
  </Card>

  <Card title="2. Setup Webhook" icon="fa-arrow-up">
    Create your endpoint to listen to merchant status events. You will need to know if the merchant is denied or approved for payment processing.
  </Card>

  <Card title="3. Signing agreement" icon="fa-check-square">
    To expedite the merchant onboarding process, I recommend we put the merchant agreement inside your application. It's an iframe docusign.
  </Card>

  <Card title="4. Add required UI for merchant" icon="fa-star">
    Inside your application, you need to provide visualization of the merchant status. We need to also add a button that allows the user to navigate to dashboard.prahsys.com.
  </Card>
</Cards>
