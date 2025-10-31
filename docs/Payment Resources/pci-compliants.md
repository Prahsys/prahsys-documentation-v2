---
title: PCI Compliants
excerpt: >-
  Payment Card Industry Data Security Standard (PCI DSS) compliance is essential
  for any business that processes credit card payments. This page explains the
  compliance requirements for different Prahsys Payments integration methods and
  how they affect your business.
deprecated: false
hidden: false
metadata:
  title: PCI Compliance Guide | Prahsys Documentation
  description: >-
    Learn about PCI DSS compliance requirements for different Prahsys Payments
    integration methods, including Self-Assessment Questionnaires (SAQs) and how
    to minimize compliance burden.
  keywords:
    - PCI DSS compliance
    - payment security
    - SAQ levels
    - Pay Portal compliance
    - Pay Session compliance
    - Pay API compliance
    - tokenization security
    - cardholder data protection
    - payment integration security
    - PCI Self-Assessment Questionnaires
  robots: index
---
## Understanding PCI DSS

PCI DSS is a set of security standards designed to ensure that all companies that accept, process, store, or transmit credit card information maintain a secure environment. The level of compliance your business needs depends on how you handle payment data.

## Self-Assessment Questionnaires (SAQs)

The PCI Security Standards Council provides different Self-Assessment Questionnaires (SAQs) depending on how your business handles cardholder data:

| SAQ Level | Description                                                                                          | Requirements  | Typical Effort        |
| --------- | ---------------------------------------------------------------------------------------------------- | ------------- | --------------------- |
| SAQ A     | For merchants that have fully outsourced all cardholder data functions                               | 22 questions  | Minimal (1-2 days)    |
| SAQ A-EP  | For e-commerce merchants using a third-party payment processor but whose website can affect security | 191 questions | Moderate (1-2 weeks)  |
| SAQ D     | For merchants that store, process, or transmit cardholder data                                       | 329 questions | Extensive (4-8 weeks) |

## Integration Methods and Compliance Requirements

Each Prahsys Payments integration method has different PCI compliance implications:

### Pay Portal (Redirect) - SAQ A

#### Lowest Compliance Burden

Eligible for the simplest SAQ A form

* **How it works**: Customers are redirected to a Prahsys-hosted payment page
* **Cardholder data**: Never touches your servers
* **PCI requirements**: SAQ A (simplest form)
* **Development effort**: Minimal
* **Best for**: Small to medium businesses wanting to minimize compliance burden

### Pay Session (Embedded Fields) - SAQ

#### Low Compliance Burden

Eligible for SAQ A when implemented correctly

* **How it works**: Payment fields are securely embedded in your website but hosted by Prahsys
* **Cardholder data**: Securely collected in iframes; never touches your servers
* **PCI requirements**: SAQ A (when implemented correctly)
* **Development effort**: Moderate
* **Best for**: Businesses wanting a customized checkout experience with minimized compliance requirements

### Pay API (Direct Integration) - SAQ D

#### Highest Compliance Burden

Requires extensive SAQ D compliance

* **How it works**: Your systems directly collect and transmit cardholder data
* **Cardholder data**: Passes through your servers
* **PCI requirements**: SAQ D (most comprehensive form)
* **Development effort**: Extensive
* **Best for**: Large enterprises with dedicated security teams and existing PCI infrastructure

## Tokenization and Compliance

Using [Tokenization](doc:tokenization) can significantly reduce your PCI compliance burden, even with the Pay API:

* Store tokens instead of actual card data
* Tokens can be used for recurring or future payments
* Significantly reduces the scope of PCI compliance requirements

## Official PCI DSS Resources

For authoritative information about PCI DSS compliance, refer to these official resources:

* <Anchor label="PCI Security Standards Council" target="_blank" href="https://www.pcisecuritystandards.org/">PCI Security Standards Council</Anchor>
* <Anchor label="PCI DSS Self-Assessment Questionnaires" target="_blank" href="https://www.pcisecuritystandards.org/document_library?category=saqs#">PCI DSS Self-Assessment Questionnaires</Anchor>

## Choosing the Right Integration Method

When selecting an integration method, consider the trade-off between customization and compliance burden:

1. **Pay Portal**: Minimal development, minimal compliance (SAQ A)
2. **Pay Session**: Moderate development, minimal compliance (SAQ A)
3. **Pay API**: Maximum flexibility, maximum compliance burden (SAQ D)

For most businesses, Pay Portal or Pay Session provides the optimal balance between user experience and compliance requirements.
