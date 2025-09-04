---
title: "How to integrate a 3rd-party payment gateway with your marketplace"
source: "https://www.sharetribe.com/docs/how-to/how-to-integrate-3rd-party-payment-gateway/"
extracted_date: "2024-05-01"
summary: "This guide describes, on a high-level, how to integrate any 3rd-party payment gateway with your Sharetribe-powered marketplace."
---

- [Prerequisites](#prerequisites)
- [Marketplace payment flow](#marketplace-payment-flow)
- [Can I just accept all payments to my own bank account and pay my providers manually?](#can-i-just-accept-all-payments-to-my-own-bank-account-and-pay-my-providers-manually)
- [Before you start the integration](#before-you-start-the-integration)
- [White-label or hosted onboarding and payments](#white-label-or-hosted-onboarding-and-payments)
- [Do you need to be PCI DSS compliant to integrate with a 3rd-party payment gateway?](#do-you-need-to-be-pci-dss-compliant-to-integrate-with-a-3rd-party-payment-gateway)
- [Communication between the marketplace app and payment gateway](#communication-between-the-marketplace-app-and-payment-gateway)
- [Integrating your Sharetribe marketplace with a 3rd-party payment gateway](#integrating-your-sharetribe-marketplace-with-a-3rd-party-payment-gateway)
- [Integrating payment steps with your marketplace](#integrating-payment-steps-with-your-marketplace)
- [Summary](#summary)
- [Further reading](#further-reading)

# How to integrate a 3rd-party payment gateway with your marketplace

This guide describes, on a high-level, how to integrate any 3rd-party payment gateway with your Sharetribe-powered marketplace.

Sharetribe provides out-of-the-box integration with Stripe. To reach markets not supported by Stripe, relying on another payment gateway can be crucial. This guide describes on a high-level, without going into the details of specific payment gateways, how to integrate any 3rd-party payment gateway (such as [PayPal Commerce Platform](https://www.paypal.com/us/business/platforms-and-marketplaces), [MANGOPAY](https://www.mangopay.com/marketplaces/), or [Adyen for Platforms](https://www.adyen.com/platform-payments)) with Sharetribe.

## Prerequisites

Before reading this guide, you should be familiar with the following Sharetribe features:

- [Privileged transitions](/docs/concepts/privileged-transitions/)
- [Events](/docs/references/events/)
- [Reacting to events](/docs/how-to/reacting-to-events/)
- [Extended data](/docs/references/extended-data/)

## Marketplace payment flow

In this section we illustrate a marketplace payment flow in high-level and briefly discuss each stage. Later in this article, we'll go through how to integrate each state with your Sharetribe-powered marketplace.

In a nutshell, a payment flow in a marketplace contains five significant steps. The following diagram illustrates a timeline of these steps:

![Marketplace payment flow](payment-flow.png)

### Step 1: Provider onboarding

In this step, the provider connects their Sharetribe account with the payment gateway. This is the step when they provide the bank details where the money from the customers will be transferred to. In addition, in this step, they provide the necessary information and documents for the identity verification and *Know Your Customer (KYC)* requirements.

### Step 2: Customer checkout

Customer checkout happens when the customer initiates a transaction. At this stage, they also provide the payment information, such as their credit card number. Also, the payment will be made at this point. The payment gateway will preauthorize the money, i.e. reserve the money on customer's credit card.

### Step 3: Provider accept

After the customer has checked out, the provider has ability to either accept or reject the request. If the request is accepted, the payment will be captured, and the reserved money will be transferred from customer's credit card to the payment gateway.

Provider accept is a step that you can combine with the customer checkout. The flow where provider accept happens instantly after customer checkout is called "instant booking" flow.

### Step 4: Customer refund

Typically, the marketplace payment flow contains a delayed payment period. This is the time between the money is captured from customer's credit card and transferred to the provider's bank account. The payout in marketplaces usually happen after the provider has successfully provided the agreed service.

Customer refund usually happens during the delayed payment period. There are many reasons why a refund may be necessary, for example, the provider or customer may not be able to make it or the provided service was not what was agreed.

### Step 5: Provider payout

If everything in the transaction went right and the customer received the agreed service, the money from the payment gateway will be eventually paid out to the provider.

## Can I just accept all payments to my own bank account and pay my providers manually?

You might be wondering if it would be easier for you to just accept the entire payment to your own bank account, and handle refunds to customers payouts to providers separately, outside the main platform functionality.

In terms of development work required to build the integration, this is indeed a lot easier. Any online payment service provider in the world supports such a simple checkout flow.

However, this can lead to a myriad of issues in terms of regulation, accounting, and liability. You could be considered as holding other people's money, which is a heavily regulated area. In many countries, you need to acquire an expensive license for this purpose, and holding money without such a license is considered a crime. You might also be considered to be responsible for providing the goods or services your providers are selling, which is not always desirable. Finally, handling the payouts to the providers correctly can be quite a lot of manual, error-prone labor. You also still need to ensure the Know Your Customer process has been done correctly for them.

Because of these challenges, we recommend you to only consider building a flow where the entire payment goes to your account only if you're absolutely sure that you're aware of all the consequences, and have gotten your approach greenlighted by your accountant and a lawyer familiar with the matter.

From now on, this article focuses on integrating with a marketplace-specific payment solution, which handles the issues described above for you. [Learn more about choosing the right payment service provider](https://www.sharetribe.com/academy/marketplace-payments/).

## Before you start the integration

Before you start coding the integration, we strongly advise you to contact the payment provider's customer support team.

Keep in mind that while many payment providers give you access to their sandbox environment, access to the live environment usually needs contacting customer support and possibly signing a contract with them.

Contact the support and make sure that:

- They are available in the country where you operate your business
- They support a marketplace-specific payment flow, handling Know Your Customer process for your providers and splitting payments
- They can process the currencies of your marketplace
- They can do payouts to your providers' country/countries
- You are eligible to get access to the live environment
- You know the process of how to get access to the live environment
- You are familiar with the fees involved.

## White-label or hosted onboarding and payments

Before integrating a 3rd-party payment gateway with Sharetribe, a few words on the different onboarding and payment experiences the payment gateways offer. The different experiences have implications for the integration, branding, and also the level of PCI-compliance required.

There are two main types of onboarding and payment experiences the payment gateways offer: white-label and hosted. However, most providers offer a mix of both.

### White-label onboarding and payments

Payment gateways such as [MANGOPAY](https://www.mangopay.com/marketplaces/) and [Adyen](https://www.adyen.com/platform-payments) offer a so-called white-label experience. This experience is closest to the default [Stripe Connect](https://stripe.com/connect) integration in Sharetribe.

A white-label experience means that you build the payment flow inside your marketplace application. This way, you have control over the user-interface and branding. The downside is that the integration requires more coding to build, most likely requires more maintenance, too. The regulations concerning online payments may change, which means you'll need to update your integration accordingly.

When using white-label experience, you may also need to do some work regarding PCI-compliance, but more on that later.

### Hosted onboarding and payments

Some payment gateways, such as PayPal, offer a hosted experience. In this case, the seller onboarding and customer payments happen by redirecting your user to the payment provider's website. After