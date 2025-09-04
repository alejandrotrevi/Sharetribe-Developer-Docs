```markdown
---
title: "How to customize pricing"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2025-07-25"
summary: "Sharetribe allows lots of flexibility for your providers in terms of how they can set their pricing. This guide walks you through customizing listing pricing and commission."
---

- [Overview](#overview)
- [Table of Contents](#table-of-contents)
  - [1. Listing extended data](#1-listing-extended-data)
  - [2. Transaction line item for insurance fee](#2-transaction-line-item-for-insurance-fee)
  - [3. Dynamic provider commission](#3-dynamic-provider-commission)

# Overview
Sharetribe allows lots of flexibility for your providers in terms of how they can set their pricing. This guide walks you through customizing listing pricing and commission.

## Table of Contents
1. [Listing extended data](#1-listing-extended-data)
2. [Transaction line item for insurance fee](#2-transaction-line-item-for-insurance-fee)
3. [Dynamic provider commission](#3-dynamic-provider-commission)

## 1. Listing extended data
This how-to guide shows you how listing pricing can be customized by using two examples: adding an insurance fee to a bookable listing, and changing provider commission so that it's based on booking length. The changes we are about to make are as follows:
1. Update rental listing data model by storing insurance fee price in listing public data.
2. Update pricing logic to add an insurance fee line item if a listing has insurance fee stored in public data.
3. Update provider commission calculation to be dependent on booking length.

For more information about pricing in Sharetribe, see the [Pricing background article](https://www.sharetribe.com/docs/concepts/pricing/).

```shell
└── src
    └── containers
        └── EditListingPricingPage
            └── EditListingPricingWizard
        └── EditListingPricingPanel
            └── EditListingPricingPanel.js
```

Booking and product related transaction processes have different pricing panels, since the product process uses the `EditListingPricingAndStockPanel` component. This means that adding the insurance fee to the `EditListingPricingPanel` only adds it to bookable listings.

However, we may want to set a different insurance fee for hourly listings and daily or nightly listings.

On submit, save price and insurance fee:

```diff
+ import { HOUR } from '../../transactions/transaction';
...
<EditListingPricingForm
 ...
 onSubmit={values => {
   const { price } = values;
   const insuranceFeeAmount = unitType === HOUR ? 500 : 2000;
   const insuranceFee = { amount: insuranceFeeAmount, currency: marketplaceCurrency };
           ...
   if (unitType === FIXED || isPriceVariationsInUse) {
     ...
     updateValues = {
       ...priceVariantChanges,
       ...startTimeIntervalChanges,
       publicData: {
         priceVariationsEnabled: isPriceVariationsInUse,
         ...startTimeIntervalChanges.publicData,
         ...priceVariantChanges.publicData,
+         insuranceFee: insuranceFee,
       },
     };
   } else {
   const priceVariationsEnabledMaybe = isBooking
     ? {
         publicData: {
           priceVariationsEnabled: false,
         },
       }
     : {};
   updateValues = {price, ...priceVariationsEnabledMaybe };
   }
}
```

## 2. Transaction line item for insurance fee
As the previous section mentions, this guide expects that the insurance fee price is stored in listing public data in an object with two keys: `amount` and `currency`. The `amount` attribute holds the price in subunits whereas `currency` holds the currency code. For example, with a cleaning fee of $20 the subunit amount is 2000 cents.

```javascript
const insuranceFeeAmount = unitType === HOUR ? 500 : 2000;
...
publicData: {
    insuranceFee: {
        amount: insuranceFeeAmount,
        currency: marketplaceCurrency
    },
}
```

Sharetribe pricing uses [privileged transitions](https://www.sharetribe.com/docs/concepts/privileged-transitions/) to ensure flexible pricing models while keeping control of the pricing logic in a secure environment. Transitioning requests of privileged transitions are made from the server-side. Thus, we'll need to update the pricing logic in the `/server/api-util/lineItems.js` file:

```shell
└── server
    └── api-util
        └── lineItems.js
```

In the helper function section of the file, add a function that resolves the insurance fee of a listing:

```javascript
const resolveInsuranceFeePrice = listing => {
  const { amount, currency } = listing.attributes.publicData?.insuranceFee || {};
  if (amount && currency) {
    return new Money(amount, currency);
  }
  return null;
};
```

Now the `transactionLineItems` function can be updated to also provide the insurance fee line item in case the listing has an insurance fee configured:

```javascript
const insuranceFeePrice = resolveInsuranceFeePrice(listing);
const insuranceFeeLineItem = insuranceFeePrice
  ? [{
      code: 'line-item/insurance-fee',
      unitPrice: insuranceFeePrice,
      quantity: 1,
      includeFor: ['customer', 'provider'],
   }]
  : [];
// Note: extraLineItems for product selling (aka shipping fee)
// is not included in either customer or provider commission calculation.
...
const lineItems = [
    order,
    ...extraLineItems,
    ...insuranceFeeLineItem,
    ...getProviderCommissionMaybe(providerCommission, order, priceAttribute),
    ...getCustomerCommissionMaybe(customerCommission, order, priceAttribute),
];
```

## 3. Dynamic provider commission
Now that we've updated the pricing logic based on listing extended data, let's next update the provider commission based on the booking length.

The idea is to keep the 10% commission defined in Console for bookings of 5 or less nights. For bookings of more than 5 nights, we'll set the following:

Update `lineItemHelpers.js` to add the new `calculateProviderCommissionPercentage` function and to update the `getProviderCommissionMaybe`:

```diff
 // Base provider and customer commissions are fetched from assets
+ const PROVIDER_COMMISSION_PERCENTAGE_REDUCTION = 3;
+ const calculateProviderCommissionPercentage = (order, providerCommission) =>
+   order.quantity > 5
+     ? providerCommission.percentage - PROVIDER_COMMISSION_PERCENTAGE_REDUCTION
+     : providerCommission.percentage;

exports.getProviderCommissionMaybe = (providerCommission, order, priceAttribute) => {
  ...
  return useMinimumCommission
    ? [{
        code: 'line-item/provider-commission',
        unitPrice: new Money(providerCommission?.minimum_amount, priceAttribute?.currency),
        quantity: getNegation(1),
        includeFor: ['provider'],
      }]
    : [{
        code: 'line-item/provider-commission',
        unitPrice: totalMoneyIn,
        percentage: getNegation(calculateProviderCommissionPercentage(order, providerCommission)),
        includeFor: ['provider'],
      }];
};
```

Then update the `transactionLineItems` function in `lineItemHelpers.js` as follows:

```javascript
const lineItems = [
  order,
  ...extraLineItems,
  ...getProviderCommissionMaybe(providerCommission, order, priceAttribute, true),
  ...getCustomerCommissionMaybe(customerCommission, order, priceAttribute),
];
```

Now when the provider takes a look at a pricing breakdown of a booking longer than 5 nights, the commission is calculated with 7% instead of 10%:

![Provider breakdown](https://www.sharetribe.com/docs/static/8ed84f2ed1ca85f7bd036658a2c744f2/fd31e/provider-breakdown.png)

> Note: Remember that Sharetribe requires the codes for custom line-items to be prefixed with _line-item/_ and the maximum length including the prefix is 64 characters. Other than that, there are no restrictions, but we recommend that _kebab-case_ is used when the code consists of multiple words.

Now, if you open up a bookable listing page and select dates in the order panel on the right, the template will fetch line items and you will see an insurance fee row in the order breakdown.
```