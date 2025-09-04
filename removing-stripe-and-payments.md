---
title: "How to remove Stripe and payments"
source: "https://www.sharetribe.com/docs/how-to/removing-stripe-and-payments/"
extracted_date: "2025-01-04"
summary: "Guide for removing Stripe integrations or all payments from Sharetribe Web Template, covering transaction process modifications, component changes, and code cleanup."
---

- [Removing Stripe](#removing-stripe)
- [1. Update the transaction processes and remove Stripe related actions](#1-update-the-transaction-processes-and-remove-stripe-related-actions)
- [2. Remove Stripe checks from EditListingWizard](#2-remove-stripe-checks-from-editlistingwizard)
- [3. Edit CheckoutPage](#3-edit-checkoutpage)
- [4. Hide/remove Stripe related pages](#4-hideremove-stripe-related-pages)
- [Extra: Removing unused exports](#extra-removing-unused-exports)
- [Removing payments](#removing-payments)
- [More changes to the transaction process](#more-changes-to-the-transaction-process)
- [Edit API calls in client app's server](#edit-api-calls-in-client-apps-server)
- [Other code changes](#other-code-changes)

# How to remove Stripe and payments

In some cases, you might want to remove Stripe integrations or even all the payments from Sharetribe Web Template. This article gives you a starting point for these customizations.

## Removing Stripe

In this guide, we will go through the minimum changes needed to make the booking flow work without Stripe. This is not an easy customization project.

The Sharetribe Web Template works with specific transaction processes and payments are a core feature of the booking and purchase processes: the ubiquitous nature means that you need to touch several components and files. Most likely you need to customize some parts (e.g. transaction process, email templates, checkout page) more depending on your marketplace idea. For example, you might also want to remove unused Stripe components from your project to clean up the code.

> **Information**
> 
> The template supports the *default-inquiry* process, which does not include bookings or stock reservations. This process is the easiest way to use a marketplace without Stripe. However, if you do want to use bookings or stock reservations without Stripe, you will need to follow the steps in this guide.

### 1. Update the transaction processes and remove Stripe related actions

You need to remove at least the following actions:

- :action/stripe-create-payment-intent
- :action/stripe-capture-payment-intent
- :action/stripe-confirm-payment-intent
- :action/stripe-create-payout
- :action/stripe-refund-charge
- :action/stripe-refund-payment

If you are using the template default processes with strong customer authentication (e.g. `default-booking` or `default-purchase`) you need to remove confirming the payment. Otherwise, the transaction will get stuck. The simplest way to do this is to remove `pending-payment` and `expire-payment` states and point `request-payment` and `request-payment-after-inquiry` directly to the following state – `preauthorized` in `default-booking` and `purchased` in `default-purchase`.

However, when you are building the transaction process to your marketplace it might make sense to rename some of the states and transitions so that they make more sense in your use-case. For instance, *preauthorized* state refers to the state of payment - it is preauthorized to be captured.

#### Edit the transaction process file to reflect the changes in your transaction process

The [src/transactions/transaction.js](https://github.com/sharetribe/web-template/blob/main/src/transactions/transaction.js) has a number of helper functions that are used to determine which state the transaction is. Depending on the transaction process being used, the *transaction.js* file refers to either *src/transactions/transactionProcessBooking.js* or *src/transactions/transactionProcessPurchase.js* for the correct states in each process. The transaction process file should be updated to match the new transaction process. [Read more about editing transaction related files](/docs/how-to/change-transaction-process-in-template/#2-update-the-relevant-files-in-srctransactions-folder).

Example:

```diff
     [STATE_INITIAL]: {
       on: {
         [TRANSITION_INQUIRE]: STATE_INQUIRY,
-        [TRANSITION_REQUEST_PAYMENT]: STATE_PENDING_PAYMENT,
+        [TRANSITION_REQUEST_PAYMENT]: STATE_PREAUTHORIZED,
       },
     },
     [STATE_INQUIRY]: {
       on: {
-        [TRANSITION_REQUEST_PAYMENT_AFTER_INQUIRY]: STATE_PENDING_PAYMENT,
+        [TRANSITION_REQUEST_PAYMENT_AFTER_INQUIRY]: STATE_PREAUTHORIZED,
       },
     },
-
-    [STATE_PENDING_PAYMENT]: {
-      on: {
-        [TRANSITION_EXPIRE_PAYMENT]: STATE_PAYMENT_EXPIRED,
-        [TRANSITION_CONFIRM_PAYMENT]: STATE_PREAUTHORIZED,
-      },
-    },
-
-    [STATE_PAYMENT_EXPIRED]: {},
     [STATE_PREAUTHORIZED]: {
       on: {
         [TRANSITION_DECLINE]: STATE_DECLINED,
```

### 2. Remove Stripe checks from EditListingWizard

In [`EditListingWizard.js`](https://github.com/sharetribe/web-template/blob/main/src/containers/EditListingPage/EditListingWizard/EditListingWizard.js) we are checking if the provider has a Stripe account with all the required information before we allow them to publish listings. This check is done in the `handlePublishListing` function and needs to be removed.

```js
  handlePublishListing(id) {
    this.props.onPublishListingDraft(id);
  }
```

After changing the `handlePublishListing` you can remove unused code. You also might want to clean up `EditListingPage` and remove the Stripe related props we pass to `EditListingWizard`.

### 3. Edit CheckoutPage

Removing Stripe from `CheckoutPage` is probably the most complicated task in this list because the Strong Customer Authentication (SCA) and ability to save payment method have added a lot of logic to the current page.

By default, the `CheckoutPage` component uses two sub-components, depending on the transaction process:

- *CheckoutPageWithInquiryProcess* is used with the default free inquiry process
- *CheckoutPageWithPayment* is used with the default booking and purchase processes

We can use an edited version of the *CheckoutPageWithPayment* component, titled *CheckoutPageWithoutPayment*, as a starting point for the modifications.

- [CheckoutPageWithoutPayment.js](/docs/c1d88f71a4d04e48e132f6ecfd9f236f/CheckoutPageWithoutPayment.js)

The *CheckoutPageWithoutPayment* component also imports a *SimpleOrderForm* component. Let's add the component to the StripePaymentForm folder.

```shell
  └── src
      └── containers
          └── CheckoutPage
              └── StripePaymentForm
                  └── SimpleOrderForm.js
```

- [SimpleOrderForm.js](/docs/d2e6ef045640604fe19471de2e3eca6e/SimpleOrderForm.js)

To use *CheckoutPageWithoutPayment*, we need to make these changes to existing components:

- Export a *processCheckoutWithoutPayment* function from *src/containers/CheckoutPage/CheckoutPageTransactionHelpers.js*

```jsx
export const processCheckoutWithoutPayment = (orderParams, extraParams) => {
  const {
    message,
    onInitiateOrder,
    onSendMessage,
    pageData,
    process,
    setPageData,
    sessionStorageKey,
  } = extraParams;

  const storedTx = ensureTransaction(pageData.transaction);

  const processAlias = pageData?.listing?.attributes?.publicData?.transactionProcessAlias;

  ////////////////////////////////////////////////
  // Step 1: initiate order                     //
  // by requesting booking from Marketplace API //
  ////////////////////////////////////////////////
  const fnRequest = fnParams => {
    // fnParams should be { listingId, deliveryMethod, quantity?, bookingDates?, protectedData }

    const requestTransition =
      sto