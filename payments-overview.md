---
title: "Payments in Sharetribe"
source: "https://www.sharetribe.com/docs/concepts/payments-overview/"
extracted_date: "2024-12-20"
summary: "This article introduces how payments work in Sharetribe in general, and describes the default Stripe payment gateway integration"
---

- [Marketplace payment flow](#marketplace-payment-flow)
- [Stripe default integration](#stripe-default-integration)
- [Sharetribe Web Template and Stripe](#sharetribe-web-template-and-stripe)
- [Frequently asked questions](#frequently-asked-questions)

# Payments in Sharetribe

This article introduces how payments work in Sharetribe in general, and describes the default Stripe payment gateway integration

Sharetribe is a full-fledged marketplace solution, complete with payment capabilities. In this article, you will learn about the Sharetribe default payment integration, implemented with Stripe, as well as some alternatives in case the default integration does not fully serve your marketplace needs.

## Marketplace payment flow

In a nutshell, a basic payment flow in a marketplace contains five significant steps:

![Payment gateway logic](payment-flow.png)

### Step 1: Provider onboarding

In this step, the provider connects their Sharetribe account with the payment gateway. This is where they provide the bank details that eventually receive money from the customers. In addition, in this step, they provide the necessary information and documents for the identity verification and *Know Your Customer* [(KYC)](https://en.wikipedia.org/wiki/Know_your_customer) requirements. [These requirements vary](https://stripe.com/docs/connect/required-verification-information) depending on the user's country of residence.

### Step 2: Customer checkout

Customer checkout happens when the customer initiates a transaction. At this stage, they also provide the payment information, such as their credit card number. Also, the payment will be made at this point. The payment gateway will preauthorize the money, i.e. reserve the money on the customer's credit card.

### Step 3: Provider acceptance

After the customer has checked out, the provider has the ability to either accept or reject the request. If the request is accepted, the payment will be captured, and the reserved money will be transferred from the customer's credit card to the payment gateway.

This is a step that you can combine with the customer checkout. The flow where the "provider accept" happens instantly after customer checkout is called [instant booking](#instant-booking) flow.

### Step 4: Customer refund

Typically, the marketplace payment flow contains a delayed payment period. This is the time between when the money is captured from the customer's credit card and when it is transferred to the provider's bank account. The payout in marketplaces usually happens after the provider has successfully provided the agreed product or service.

Customer refund usually happens during this delayed payment period. There are many reasons why a refund may be necessary. For example, the provider or customer may not be able to make it to a booked event, or the provided product or service was not what was agreed.

### Step 5: Provider payout

If everything in the transaction went right and the customer received the agreed product or service, the money from the payment gateway will be eventually paid out to the provider.

## Stripe default integration

In the default Sharetribe transaction process and Sharetribe Web Template, the steps described above are implemented using Stripe. The integration uses [Stripe Custom Connect accounts](https://stripe.com/docs/connect/custom-accounts) for providers. The customer can check out using a payment card or [another supported method](#payment-methods-and-currencies), and they can also save their payment method for future use. The integration uses [Stripe destination charges](https://stripe.com/docs/connect/destination-charges) ([on behalf of the provider](https://stripe.com/docs/payments/connected-accounts)) to collect the payment from the customer to the provider's Custom Connect account first, and the possible commission is then transferred to the platform account as an [application fee](https://stripe.com/docs/connect/destination-charges#application-fee). Once the transaction is successfully over, the provider's share is paid out to the bank account that the provider gave upon onboarding.

> **What does "destination charge" mean?**
> 
> The Stripe on_behalf_of destination charge means that when the charge is created, the money goes directly to the provider's Custom Connect account in Stripe, and the provider's information is shown on the customer's payment method receipt. This also means that a listing cannot be booked or purchased if the provider has not onboarded to Stripe, because the charge cannot be created in Stripe without the provider's Custom Connect account information.

### Default payment process with Stripe

![Default payment flow in Sharetribe](automatic_confirmation_flow.png)

#### 1. Provider onboarding

In the Sharetribe default integration, users need to have a Stripe account with a bank account set up before others can initiate transactions with them successfully. This is done by [creating a Stripe account](https://www.sharetribe.com/api-reference/marketplace.html#create-stripe-account) for the authenticated user. The [Sharetribe Web Template](/docs/concepts/providers-and-customers-on-stripe-platform/) is configured to do this step out-of-the-box using [Stripe Connect Onboarding](https://stripe.com/en-fi/connect/onboarding).

To create the account, Stripe requires verification information from the provider, and the specific types of verification information depend on the provider's country. You can check the verification requirements for your most likely marketplace provider demographics in [Stripe's own documentation](https://stripe.com/docs/connect/required-verification-information).

#### 2. Customer checkout

When the customer initiates a transaction in the Sharetribe default transaction processes, Sharetribe creates a [PaymentIntent](/docs/concepts/payment-intents/) for the total price of the transaction. Once the PaymentIntent is confirmed, Stripe preauthorizes the sum from the customer's payment method. In other words, even though the sum is not paid out from the customer's card, it is reserved and not available to be used by the customer.

The preauthorization is valid for 7 days, after which the preauthorization is automatically released by Stripe, and the funds are again available to the customer.

In the Sharetribe Web Template default purchase process, creating and confirming the PaymentIntent are both triggered at the same customer action.

**Related Stripe actions:**

- [:action/stripe-create-payment-intent](/docs/references/transaction-process-actions/#actionstripe-create-payment-intent)
- [:action/stripe-confirm-payment-intent](/docs/references/transaction-process-actions/#actionstripe-confirm-payment-intent)

#### 3. Provider acceptance

The default purchase process in the Sharetribe Web Template uses an instant checkout, which means customer checkout and purchase acceptance are all triggered on the same customer action. In other words, provider acceptance is not necessary. However, it is possible to add the provider acceptance step to the product buying process as well.

In the default booking process, on the other hand, a provider has 6 days to accept the booking until it expires automatically. This timeline ensures that the Stripe preauthorization does not expire before the provider has the opportunity to accept or reject the booking. Once the provider accepts the booking, the PaymentIntent is captured and the transaction sum is transferred from the customer's card to the provider's Custom Connect account. If the transaction has any commissions, those are then paid from the provider's Connect account to the platform's account as an [application fee](https://stripe.com/docs/api/application_fees).

Depending on how the [transaction's line items](/docs/concepts/pricing/#line-items) have been defined, the platform can take a [commission of the price](/docs/concepts/commissions-and-monetizing-your-platform/) from either the provider, the customer, or both. The platform is also responsible for paying all [Stripe fees](https://stripe.com/en-fi/connect/pricing) related to the Custom Connect account usage, so the commissions must be defined to cover those expenses as well.

**Related Stripe actions:**

- [:action/stripe-capture-payment-intent](/docs/references/transaction-process-actions/#actionstripe-capture-payment-intent)

#### 4. Customer refund

If the customer requests a refund for one reason or another, the operator can refund the PaymentIntent. The Sharetribe integration with Stripe only supports full refunds. (Handling partial refunds is discussed [later in this article](#how-can-i-partially-refund-transactions-in-my-sharetribe-marketplace).) The default transaction process takes into account whether or not the PaymentIntent has already been captured from the customer's account.

**Related Stripe actions:**

- [:action/stripe-refund-payment](/docs/references/transaction-process-actions/#actionstripe-refund-payment)