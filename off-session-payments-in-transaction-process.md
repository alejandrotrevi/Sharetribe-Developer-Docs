```markdown
---
title: "Automatic off-session payments in transaction process"
source: "provided"
extracted_date: "2023-10-24"
summary: "With off-session payments, you can automatically charge your customers closer to their booking times."
---

- [Overview](#overview)
- [Table of Contents](#table-of-contents)
- [Transaction process example](#transaction-process-example)
- [Considerations about implementation in Sharetribe Web Template](#considerations-about-implementation-in-sharetribe-web-template)
  - [Transitions and states](#transitions-and-states)
  - [Separating order from payment](#separating-order-from-payment)
  - [Handling delayed manual payment](#handling-delayed-manual-payment)

# Overview
With off-session payments, you can automatically charge your customers closer to their booking times. This allows for money to be held in Stripe throughout the booking period even when the booking is placed long time in advance.

## Table of Contents
- [Transaction process example](#transaction-process-example)
- [Considerations about implementation in Sharetribe Web Template](#considerations-about-implementation-in-sharetribe-web-template)
- [Transitions and states](#transitions-and-states)
- [Separating order from payment](#separating-order-from-payment)
- [Handling delayed manual payment](#handling-delayed-manual-payment)

## Transaction process example
Suppose your sauna rentals marketplace should allow customers to book saunas up to a year in advance, but the customer is only charged at a specified moment before the booking. The figure below illustrates how a part of your transaction process might look like. The [example-processes Github repository](https://github.com/sharetribe/example-processes/) contains [an example transaction process](https://github.com/sharetribe/example-processes/tree/master/automatic-off-session-payment) corresponding to the flow described.

![Example transaction process with delayed payment](/docs/static/d2fd2f671347b00d8b4bfa745e2cd783/53fb6/tx-delayed-payments.png)

In this example, a transaction goes as follows:
1. The customer picks the desired booking dates and initiates a transaction. The price of the transaction is calculated, but no preauthorization or payment is made at this point.
2. The provider verifies the request and accepts the booking.
3. At a later point in time (1 month before the booking start time in this example, and 1 day before the booking start in the [example process](https://github.com/sharetribe/example-processes/tree/master/automatic-off-session-payment)), an attempt is made to automatically charge the customer’s stored payment card. If the charge succeeds, the transaction continues onwards.
4. The automatic charge can fail for multiple reasons. If the charge fails, the customer (and optionally also the provider) receives an email notification and the customer is asked to visit the marketplace website in order to pay manually.

### How does creating and capturing an off-session payment work?
In the auto-payment transition, the payment intent creation needs to be configured to use the customer’s saved payment information, if it exists. When the action is configured like this, it both creates and confirms the payment intent. Therefore, only capturing the payment intent remains necessary.

```clojure
{
  :name :transition/auto-payment,
  :from :state/pending-payment,
  :to :state/paid,
  :at {
    :fn/plus [
      {
        :fn/timepoint [:time/first-entered-state :state/pending-payment]
      }
      {
        :fn/period ["PT5M"]
      }
    ]
  },
  :actions [
    {:name :action/stripe-create-payment-intent
      :config {:use-customer-default-payment-method? :true}}
    {:name :action/stripe-capture-payment-intent}
  ]
}
```

It is important to note that an off-session payment can fail for various reasons:
- The card could be denied due to insufficient funds.
- The issuing bank may require additional authentication from the customer.
- The payment card might have expired.

It is therefore always important to allow for a fallback payment path in your transaction process.

## Considerations about implementation in Sharetribe Web Template
If you want to implement [the example process](https://github.com/sharetribe/example-processes/tree/master/automatic-off-session-payment) in your user interface, there are multiple ways to do so. If your user interface is based on the Sharetribe Web Template, here are a few things worth considering.

### Transitions and states
[Transitions and states](https://www.sharetribe.com/docs/tutorial/create-transaction-process/#update-client-app) are used in the template as conditions for several behaviors, including redirects and displayed content. The [transaction resource](https://www.sharetribe.com/api-reference/marketplace.html#transaction-resource-format) contains information about the transaction's last transition and its timestamp.

### Separating order from payment
In the default transaction process and default template flow, the order is initiated and processed in [CheckoutPageWithPayment.js](https://github.com/sharetribe/web-template/blob/main/src/containers/CheckoutPage/CheckoutPageWithPayment.js) using `processCheckoutWithPayment()`. The [ListingPage.shared.js](https://github.com/sharetribe/web-template/blob/main/src/containers/ListingPage/ListingPage.shared.js) passes initial values in its `handleSubmit()` with `callSetInitialValues()`, and those initial values get handled on the checkout page.

### Handling delayed manual payment
If the automatic payment succeeds, the customer does not need to take further action on the transaction before the review process. Manual payment, on the other hand, does require a new user flow in the template. [CheckoutPage.js](https://github.com/sharetribe/web-template/blob/main/src/containers/CheckoutPage/CheckoutPage.js) is set up to handle payments toward Stripe.

Pay attention to the following points when designing your user flow:
- What action does the customer take in the UI to initiate manual payment?
- Does the provider see whether or not the customer has paid for the booking?

## Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.

```