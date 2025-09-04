```markdown
---
title: "Commissions and Monetizing Your Platform"
source: "provided"
extracted_date: "2025-09-04"
summary: "Sharetribe provides configurable options for monetizing your platform."
---

- [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Percentage-based commissions](#percentage-based-commissions)
    - [Example](#example)
  - [Fixed commissions](#fixed-commissions)
    - [Example](#example-1)
  - [Dynamically calculated commissions](#dynamically-calculated-commissions)
    - [Example](#example-2)
  - [Subscription-based model](#subscription-based-model)

# Introduction
Sharetribe provides configurable options for monetizing your platform. 
You can collect commissions from providers, customers, or both.

## Table of Contents
1. [Introduction](#introduction)
2. [Percentage-based commissions](#percentage-based-commissions)
3. [Fixed commissions](#fixed-commissions)
4. [Dynamically calculated commissions](#dynamically-calculated-commissions)
5. [Subscription-based model](#subscription-based-model)

# Percentage-based commissions
One of the simplest ways to configure commissions is to define a percentage of the listing price as a commission. Commissions can be charged from the provider, the customer, or both.

## Example
*This example illustrates how to use percentage-based commissions without Console-based hosted configurations.*

```javascript
const { calculateTotalFromLineItems } = require('./lineItemHelpers');

const PROVIDER_COMMISSION_PERCENTAGE = -12; // Provider commission is negative
const CUSTOMER_COMMISSION_PERCENTAGE = 10;   // Customer commission is positive

const order = {
  code,
  unitPrice,
  quantity,
  includeFor: ['customer', 'provider'],
};

const providerCommissionPercentage = {
  code: 'line-item/provider-commission',
  unitPrice: calculateTotalFromLineItems([order]),
  percentage: PROVIDER_COMMISSION_PERCENTAGE,
  includeFor: ['provider'],
};

const customerCommissionPercentage = {
  code: 'line-item/customer-commission',
  unitPrice: calculateTotalFromLineItems([order]),
  percentage: CUSTOMER_COMMISSION_PERCENTAGE,
  includeFor: ['customer'],
};

const lineItems = [
  order,
  providerCommissionPercentage,
  customerCommissionPercentage,
];
```

For a 100 EUR listing, this would result in a 110 EUR payin for the customer and an 88 EUR payout for the provider. The marketplace would receive 22 EUR minus Stripe fees.

### Negative or positive commission?
Commission line items are defined as either positive or negative depending on the transaction party.
- Provider commission is defined as **negative**, since the provider's total is the listing price minus the provider commission.
- Customer commission is defined as **positive**, since the customer's total is the listing price plus the customer commission.

# Fixed commissions
In addition to percentages, you can define commissions with fixed sums as the `unitPrice` of the line item using `quantity` instead of `percentage`.

## Example
Both the provider and customer pay a fixed commission regardless of the listing price or quantity, and Console-based commissions are ignored.

```javascript
const FIXED_PROVIDER_COMMISSION = -1500; // Provider commission is negative
const FIXED_CUSTOMER_COMMISSION = 1050;   // Customer commission is positive

const calculateCommission = (unitPrice, amount) => {
  return new Money(amount, unitPrice.currency);
};

const order = {
  code,
  unitPrice,
  quantity,
  includeFor: ['customer', 'provider'],
};

const providerCommissionFixed = {
  code: 'line-item/provider-commission',
  unitPrice: calculateCommission(unitPrice, FIXED_PROVIDER_COMMISSION),
  quantity: 1,
  includeFor: ['provider'],
};

const customerCommissionFixed = {
  code: 'line-item/customer-commission',
  unitPrice: calculateCommission(unitPrice, FIXED_CUSTOMER_COMMISSION),
  quantity: 1,
  includeFor: ['customer'],
};

const lineItems = [
  order,
  ...extraLineItems,
  providerCommissionFixed,
  customerCommissionFixed,
];
```

For a 100 EUR listing, this would result in a 110.5 EUR payin for the customer and an 85 EUR payout for the provider. The marketplace would receive 25.5 EUR minus Stripe fees.

# Dynamically calculated commissions
You can also calculate the commissions with more complex logic. You can set the result of the calculation as either the `unitPrice` or the `percentage` of the line item.

## Example
Add the following to `lineItemHelpers.js`:

```javascript
const MINIMUM_PROVIDER_COMMISSION = -1000; // Negative commission in minor units
const CUSTOMER_COMMISSION_PERCENTAGE_REDUCTION = 3;

const calculateProviderCommission = (order, providerCommission) => {
  const price = this.calculateTotalFromLineItems([order]);
  const commission = calculateTotalPriceFromPercentage(price, providerCommission);

  if (commission.amount < MINIMUM_PROVIDER_COMMISSION) {
    return commission;
  }

  return new Money(MINIMUM_PROVIDER_COMMISSION, price.currency);
};

const calculateCustomerCommissionPercentage = (order, customerCommission) => {
  return order.quantity > 4 ?
    customerCommission.percentage - CUSTOMER_COMMISSION_PERCENTAGE_REDUCTION :
    customerCommission.percentage;
};

exports.getDynamicProviderCommissionMaybe = (order, providerCommission) => {
  return this.hasCommissionPercentage(providerCommission) ?
    [{
      code: 'line-item/provider-commission',
      unitPrice: calculateProviderCommission(order, getNegation(providerCommission.percentage)),
      quantity: 1,
      includeFor: ['provider'],
    }] : [];
};

exports.getDynamicCustomerCommissionMaybe = (order, customerCommission) => {
  return this.hasCommissionPercentage(customerCommission) ?
    [{
      code: 'line-item/customer-commission',
      unitPrice: this.calculateTotalFromLineItems([order]),
      percentage: calculateCustomerCommissionPercentage(order, customerCommission),
      includeFor: ['customer'],
    }] : [];
};
```

Then you can use the function like this in `lineItems.js`:

```javascript
const { getDynamicProviderCommissionMaybe, getDynamicCustomerCommissionMaybe } = require('./lineItemHelpers');

const order = {
  code,
  unitPrice,
  quantity,
  includeFor: ['customer', 'provider'],
};

const lineItems = [
  order,
  ...extraLineItems,
  ...getDynamicProviderCommissionMaybe(order, providerCommission),
  ...getDynamicCustomerCommissionMaybe(order, customerCommission),
];
```

# Subscription-based model
The line item commissions are the most straightforward way of monetizing your marketplace and are directly supported by Sharetribe. However, you might want to experiment with other monetization models depending on your business idea. For example, subscriptions might be a good way of monetizing your marketplace. With the [Integration API](https://www.sharetribe.com/docs/concepts/marketplace-api-integration-api/#when-to-use-the-integration-api), you can integrate a third-party service such as [Chargebee](https://www.chargebee.com/) or [Stripe billing](https://stripe.com/en-fi/billing) to process subscription payments from users who want access to your marketplace.

## Can’t find what you’re looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.

```