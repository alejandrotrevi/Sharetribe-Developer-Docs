```markdown
---
title: "How saving a payment card works in the Sharetribe Web Template"
source: "provided"
extracted_date: "2023-10-24"
summary: "An overview of the Sharetribe Web Template functionality for storing payment cards."
---

- [Table of Contents](#table-of-contents)
  - [Saving card details](#saving-card-details)
  - [Saving cards without making an initial payment](#saving-cards-without-making-an-initial-payment)
  - [Saving cards after a payment](#saving-cards-after-a-payment)
  - [Getting permission to save a card](#getting-permission-to-save-a-card)
  - [Charging Saved Cards](#charging-saved-cards)
  - [Saving Payment Card with PaymentIntents payment flow](#saving-payment-card-with-paymentintents-payment-flow)
  - [Initial data for Checkout](#initial-data-for-checkout)
  - [StripePaymentForm changes - show correct form fields](#stripepaymentform-changes---show-correct-form-fields)
  - [Submit StripePaymentForm](#submit-stripepaymentform)

# How saving a payment card works in the Sharetribe Web Template

An overview of the Sharetribe Web Template functionality for storing payment cards.

## Saving card details

There are 2 ways to save payment card details:
- Saving cards details without making an initial payment.
- Saving cards details after a payment.

The former can be done on the payment methods page, but you can also save payment card details on the checkout page when making a one-time payment.

### Saving cards without making an initial payment

If the user has no existing default payment card, `PaymentMethodsPage` will show just a form to save payment card details:

![Payment methods page without default payment method.](https://www.sharetribe.com/docs/static/d0bd1f1740550d34b4d147099d901c38/53fb6/payment-methods-page.png)

If there is a default payment method saved, the user will see a dropdown where the current method is selected. Options are to replace the current payment card or delete the current payment card.

![Payment methods page with default payment method.](https://www.sharetribe.com/docs/static/527d965601799dde6c39f9ea7561f532/53fb6/payment-methods-page-saved-card.png)

Under the hood, saving a new payment method needs to complete the following steps:
1. Create Stripe [Setup Intent](https://www.sharetribe.com/api-reference/marketplace.html#stripe-setup-intents) through Sharetribe API to obtain the client secret.
2. Call `stripe.handleCardSetup` with the client secret, which will handle user actions like 3D Secure authentication.
3. Save payment method:
   - Create a new Stripe Customer if needed and attach the new payment method.
   - To replace a payment card, one needs to remove the previous card first and then add a new payment method.

### Saving cards after a payment

If the user doesn't have a default payment card saved, or if she is making a one-time payment with a different payment card, there's an option to save the new payment card as the default payment method.

![One-time payment and saving card details for future use](https://www.sharetribe.com/docs/static/70f5fefc2341c31f2bc35c99a0cb209f/53fb6/one-time-payment.png)

If the default payment method exists, the user needs to select one-time payment first to replace the default card.

Under the hood, changes made to the Payment Intents checkout flow:
- Determine if the user has a saved default payment method.
- Show the correct form fields:
  - If there’s a default payment method saved, show SavedCardDetails component.
  - If there’s no default payment method saved, show one-time payment form.
- When requesting payment, include parameter if card details are being saved.
- Call `stripe.confirmCardPayment`, handle payment transitions, and send initial messages as before.
- Save the payment method if the user has given permission.

## Getting permission to save a card

Once your payment flow is set up to properly save a card with the Payment Intents or Setup Intents API, Stripe will mark any subsequent off-session payment as a merchant-initiated transaction to reduce the need to authenticate. Merchant-initiated transactions require an agreement (also known as a “mandate”) between you and your customer. Read more about needed permissions from [Stripe's documentation](https://stripe.com/docs/payments/cards/reusing-cards#mandates).

## Charging Saved Cards

If the user has a default payment method and chooses to use it to book a listing, the following changes are needed:
- The id of Stripe's payment method needs to be sent as `paymentMethod` when requesting payment.
- `stripe.confirmCardPayment`: Stripe Elements (card) is not needed if the default payment method is used.

## Saving Payment Card with PaymentIntents payment flow

Here’s the description of the complete call sequence on CheckoutPage:

1. **Step 1.** 
   ```javascript
   sdk.transactions.initiate({
     processAlias,
     transition: 'transition/request-payment',
     ...
   });
   ```

   - API creates PaymentIntent against Stripe API and returns `stripePaymentIntentClientSecret`.
   - Booking is created, and availability management will block dates for conflicting bookings.
   - Automatic expiration happens in 15 minutes if the process is not transitioned with `transition/confirm-payment` before that.
   - After this call, the newly created transaction is saved to session storage.

   When saving card details, pass parameter `setupPaymentMethodForSaving`.

   ```javascript
   sdk.transactions.initiate({
     processAlias,
     transition: 'transition/request-payment',
     params: {
       listingId,
       bookingStart,
       bookingEnd,
       setupPaymentMethodForSaving: true,
     },
   });
   ```

2. **Step 2.** 
   ```javascript
   stripe.confirmCardPayment(
     stripePaymentIntentClientSecret,
     paymentParams
   );
   ```

   `paymentParams` should include card element and billing details. See [stripe.confirmCardPayment documentation](https://stripe.com/docs/js/payment_intents/confirm_card_payment).

3. **Step 3.** 
   ```javascript
   sdk.transactions.transition({
     id: transactionId,
     transition: 'transition/confirm-payment',
     params,
   });
   ```

4. **Step 4.** 
   ```javascript
   sdk.messages.send({
     transactionId: orderId,
     content: message
   });
   ```

5. **Step 5.** 
   Here the payment method needs to be saved. Depending on the current state, the following scenarios may require different API calls:

   - **No StripeCustomer entity connected:** 
     ```javascript
     sdk.stripeCustomer.create({
       stripePaymentMethodId
     }, {
       expand: true,
       include: ['defaultPaymentMethod']
     });
     ```

   - **Current user has defaultPaymentMethod (2 calls):** 
     ```javascript
     => sdk.stripeCustomer.deletePaymentMethod({}, {
       expand: true
     });
     => sdk.stripeCustomer.addPaymentMethod({ stripePaymentMethodId }, {
       expand: true
     });
     ```

   - **Current user has connected StripeCustomer but no defaultPaymentMethod:** 
     ```javascript
     => sdk.stripeCustomer.addPaymentMethod({ stripePaymentMethodId }, {
       expand: true
     });
     ```

After these steps, the customer is redirected to their inbox. Depending on the transaction process, either the purchase is accepted directly, or it is up to the provider to accept or decline the booking.

---

### Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```