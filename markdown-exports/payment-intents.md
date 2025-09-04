---
title: "How PaymentIntents work"
source: "https://www.sharetribe.com/docs/concepts/payment-intents/"
extracted_date: "2025-01-04"
summary: "Overview of how Stripe PaymentIntents work with Sharetribe, and how you can build your transaction process with support for Strong Customer Authentication (SCA)."
---

- [Introduction](#introduction)
- [Transaction process with PaymentIntents](#transaction-process-with-paymentintents)
  - [Example transaction process with card payments](#example-transaction-process-with-card-payments)
  - [Example transaction process with both card and push payments](#example-transaction-process-with-both-card-and-push-payments)
- [Actions related to PaymentIntents](#actions-related-to-paymentintents)
  - [stripe-create-payment-intent](#stripe-create-payment-intent)
  - [stripe-create-payment-intent-push](#stripe-create-payment-intent-push)
  - [stripe-confirm-payment-intent](#stripe-confirm-payment-intent)
  - [stripe-capture-payment-intent](#stripe-capture-payment-intent)
  - [stripe-refund-payment](#stripe-refund-payment)
- [Required actions in the client](#required-actions-in-the-client)
  - [Handling Strong Customer Authentication](#handling-strong-customer-authentication)
  - [Implementing the PaymentIntent flow](#implementing-the-paymentintent-flow)
- [Using PaymentIntents in Sharetribe](#using-paymentintents-in-sharetribe)
- [Further reading](#further-reading)

# How PaymentIntents work

*Last updated October 24, 2023*

Overview of how Stripe PaymentIntents work with Sharetribe, and how you can build your transaction process with support for Strong Customer Authentication (SCA).

## Introduction

[PaymentIntents](https://stripe.com/docs/payments/payment-intents) are a mechanism provided by Stripe to track the lifecycle of customer checkout flow. In addition, PaymentIntents provide tools for [Strong Customer Authentication (SCA)](/docs/concepts/strong-customer-authentication/) where required. Sharetribe has built-in support for PaymentIntents and Strong Customer Authentication.

In September 2019 [new European regulation](https://stripe.com/en-fi/payments/strong-customer-authentication) will begin requiring SCA for online payments from European customers. In order to conform to these regulatory changes, Stripe encourages users to [migrate or use PaymentIntents](https://stripe.com/docs/strong-customer-authentication/migration) instead of directly creating Charges.

In the future, PaymentIntents can be required in other countries outside Europe due to local regulatory changes but they can already be used outside Europe. And if needed, they can provide fraud prevention with things like [3D Secure Card Payments](https://stripe.com/docs/payments/3d-secure).

PaymentIntents also allow a variety of payment methods to be used when making a payment in Sharetribe. See the [overview of supported payment methods in Sharetribe](/docs/concepts/payment-methods-overview/).

This article will describe how PaymentIntents relate to Sharetribe transaction processes and the general principles of implementing a checkout flow with PaymentIntents.

## Transaction process with PaymentIntents

On high level, the payment flow with PaymentIntents has the following steps:

1. Customer initiates (or transitions) a transaction with a transition containing action that creates a PaymentIntent.
2. Customer uses the PaymentIntent data to complete any steps necessary to authenticate and authorize the payment.
3. The transaction can proceed only after customer has authorized (if required) the payment. The PaymentIntent is confirmed, resulting in a Charge being preauthorized (in the case of [card payments](/docs/concepts/payment-methods-overview/#card-payment-flow)) or fully captured (in the case of [push payment methods](/docs/concepts/payment-methods-overview/#push-payment-flow)).
4. Transaction flow continues as usual onwards.

### Example transaction process with card payments

![Automatic PaymentIntent flow](automatic_confirmation_flow.png)

For technical implementation of PaymentIntents, Stripe offers two approaches - [manual or automatic confirmation flow](https://stripe.com/docs/payments/payment-intents#one-time-payments). Sharetribe uses the automatic flow. In practice, the Sharetribe transaction engine models the automatic flow with two transitions. First transition creates the PaymentIntent (Step 1.) and second transition will validate and mark it confirmed in Sharetribe (Step 3.). Between these steps, the automatic flow pushes the responsibility of authenticating, authorizing and confirming the payment in Stripe to the client application (Step 2.). More information on the Step 2. can be found in this [section](#required-actions-in-the-client).

### Example transaction process with both card and push payments

![PaymentIntent process with card and push payments](push-payment-process.png)

Since push payments [do not have a preauthorization stage](/docs/concepts/payment-methods-overview/#push-payment-flow), this process allows an instant-booking type of flow, where the booking does not need acceptance from the provider.

You can find another example process with only an *instant booking* flow and support for both card and push payments in the [Instant booking process](https://github.com/sharetribe/example-processes#instant-booking) in the [Sharetribe example transaction processes repository](https://github.com/sharetribe/example-processes).

## Actions related to PaymentIntents

The following actions can be attached to a transaction process in order to implement PaymentIntent flow and are already present in the default flows.

### stripe-create-payment-intent

Creates a PaymentIntent for use with card payments (or payment methods that are similar, such as Google Pay or Apple Pay). You can optionally pass in a [PaymentMethod](https://stripe.com/docs/payments/payment-methods) ID, or attach a PaymentMethod later to the transaction during the validation and confirmation in the client by using Stripe Elements. The latter is the recommended way and is covered in the [implementation guide](#implementing-the-paymentintent-flow).

For detailed reference, see [here](/docs/references/transaction-process-actions/#actionstripe-create-payment-intent).

### stripe-create-payment-intent-push

Creates a PaymentIntent for use with push payments. You can optionally pass in a [PaymentMethod](https://stripe.com/docs/payments/payment-methods) ID, or attach a PaymentMethod later to the transaction during the validation and confirmation in the client by using Stripe Elements. The latter is the recommended way and is covered in the [implementation guide](#implementing-the-paymentintent-flow).

For detailed reference, see [here](/docs/references/transaction-process-actions/#actionstripe-create-payment-intent-push).

### stripe-confirm-payment-intent

Validates that the transaction has a PaymentIntent created and verifies via Stripe API that the PaymentIntent status is `requires_capture`, `requires_confirmation` or `succeeded` (only allowed for push payment methods). Confirms the PaymentIntent in Stripe, if needed.

If the payment intent was created with `stripe-create-payment-intent` (a card payment), a preauthorization is placed on the card. The payment then can be captured in full by using `stripe-capture-payment-intent` within 7 days of creating the payment intent, or the preauthorization can be released by using `stripe-refund-payment`.

On the other hand, if the payment intent was created with `stripe-create-payment-intent-push`, there is no preauthorization, the payment is captured in full and there is no need to use the `stripe-capture-payment-intent` action. The payment can be refunded in full using the `stripe-refund-payment`.

For detailed reference, see [here](/docs/references/transaction-process-actions/#actionstripe-confirm-payment-intent).

### stripe-capture-payment-intent

Captures a confirmed PaymentIntent. In case of PaymentIntents created through `stripe-create-payment-intent-push`, the PaymentIntent is automatically captured already when confirmed and this action has no effect. Note that uncaptured payment intents are valid for seven days, after which they are automatically canceled by Stripe.

For detailed reference, see [here](/docs/references/transaction-process-actions/#actionstripe-capture-payment-intent).

### stripe-refund-payment

Either cancels an unconfirmed PaymentIntent or refunds the related captured charge.

For detailed reference, see [here](/docs/references/transaction-process-actions/#actionstripe-refund-payment).

## Required actions in the client

The required actions in the client are related to authentication and confirmation. You nee