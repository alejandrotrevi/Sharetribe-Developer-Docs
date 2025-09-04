---
title: "Providers and customers on your Stripe Platform"
source: "https://www.sharetribe.com/docs/concepts/providers-and-customers-on-stripe-platform/"
extracted_date: "2025-01-09"
summary: "This article describes how providers and customers show up on your Stripe platform account."
---

- [Introduction](#introduction)
- [Stripe for providers: Stripe Connect Onboarding](#stripe-for-providers-stripe-connect-onboarding)
- [Stripe Connect Custom Accounts](#stripe-connect-custom-accounts)
- [Fetching an existing Stripe Connect Account](#fetching-an-existing-stripe-connect-account)
- [Creating a new Stripe Connect Account](#creating-a-new-stripe-connect-account)
- [Handling verification status – Stripe Account Links](#handling-verification-status--stripe-account-links)
- [Stripe Connect Account and Onboarding in Sharetribe Web Template](#stripe-connect-account-and-onboarding-in-sharetribe-web-template)
- [Using custom flow for Stripe provider onboarding](#using-custom-flow-for-stripe-provider-onboarding)
- [Stripe for customers: saved payment methods](#stripe-for-customers-saved-payment-methods)
- [Changing a marketplace's Stripe platform account](#changing-a-marketplaces-stripe-platform-account)

# Providers and customers on your Stripe Platform

This article describes how providers and customers show up on your Stripe platform account.

## Introduction

If your marketplace handles payments through the Sharetribe Stripe integration, your providers and customers will show up in different ways on your Stripe platform account. The main ways this happens is through Stripe Connect accounts and Stripe Customers.

The Sharetribe Stripe integration uses Stripe Custom Connect accounts for providers. When a provider first creates a listing, they need to onboard to Stripe Connect before they can receive payouts. For customers, on the other hand, a Stripe Customer does not need to be created by default.

## Stripe for providers: Stripe Connect Onboarding

When your marketplace handles payments, it is important to have a provider verification process where providers can enter all necessary and required information for them to receive payments. Provider verification is [a requirement from Stripe's side](https://support.stripe.com/questions/know-your-customer-obligations) for identity verification, risk assessment, and avoiding money laundering and other types of financial crime.

Regulatory aspects of provider onboarding can be challenging and changing rapidly. Stripe Connect Onboarding provides ready tools for meeting the requirements and reducing the operational complexity of self-managing the onboarding flow and identity verification.

For both Sharetribe Web Template and custom implementations, your marketplace will first need to set up Stripe for payments and enable Connect Onboarding. You can review the [instructions in our Help Center](https://www.sharetribe.com/help/en/articles/8413086-how-to-set-up-stripe-for-payments-on-your-marketplace) for more details.

### Stripe Connect Custom Accounts

Sharetribe uses Stripe Connect with Custom Accounts as the default payment integration. These enable your sellers to process payments through your marketplace. Stripe Connect also has other types of accounts, such as Standard and Express, but those are not compatible with the Sharetribe Stripe integration.

When you use the Sharetribe Stripe integration, you need to create Custom Connect accounts for your users through the Sharetribe Marketplace API [Stripe Account creation endpoint](https://www.sharetribe.com/api-reference/marketplace.html#create-stripe-account). It is not possible to associate an existing Custom Connect account with a Sharetribe user profile.

When you need to update the Stripe Account, you can either use the Marketplace API [Stripe Account updating endpoint](https://www.sharetribe.com/api-reference/marketplace.html#update-stripe-account) or directly through [Stripe API](https://docs.stripe.com/api/accounts/update).

### Fetching an existing Stripe Connect Account

If the provider already has a Stripe account, the [currentUser resource](https://www.sharetribe.com/api-reference/marketplace.html#currentuser-resource-format) has a *stripeConnected* boolean flag set to *true.* To fetch the provider's existing Stripe Connect account details, we can use the Marketplace API [Stripe Account fetch](https://www.sharetribe.com/api-reference/marketplace.html#fetch-stripe-account) endpoint. This will allow you to alert the provider if there is some required information missing from their Stripe Account.

Stripe account data is returned after each create and update Stripe Account API call, so there is no need for separate fetch API call in these cases.

After the Stripe Account has been fetched, you need to check *requirements* in the *stripeAccountData* attribute, which contains the related [Stripe Account Object](https://stripe.com/docs/api/accounts/object). If there are any fields in *past_due* or *currently_due* it means that those fields need to be collected to keep the account enabled. In other words, there are requirements missing. If there are no fields in *past_due* or *currently_due*, it means that the verification is completed for now. It is still possible that there might be new fields to be collected if the account reaches the next volume thresholds.

### Creating a new Stripe Connect Account

If the current user's *stripeConnected* flag is false, the current user does not have a Stripe account, and you need to create a new Stripe Connect account for the user.

If you collect bank account information in the client, [create a Stripe bank account token](https://docs.stripe.com/api/tokens/create_bank_account) so that you can securely pass it to the Sharetribe backend.

If you collect other information in the client, such as account type (individual vs business) or whether the user has accepted Stripe's Connected Account Agreement, [create a Stripe account token](https://docs.stripe.com/api/tokens/create_account) to pass that information to the Sharetribe backend.

With the Sharetribe Marketplace API, [create a Stripe account](https://www.sharetribe.com/api-reference/marketplace.html#create-stripe-account) and pass any tokens you created in the previous steps to the endpoint. The only mandatory parameter to create a Stripe Connect account through the Marketplace API is the country of the account, and it cannot be modified after the account has been created.

In addition to the country information, we recommend passing the *requestedCapabilities* parameter – the required capabilities for payments to work in Sharetribe are *card_payments* and *transfers*, so you will need to define these for the account for it to work with the Sharetribe Stripe integration. There are also other optional parameters that you can collect in your application and pass to the endpoint. If you don't pass these details, the necessary information will be collected in the Stripe Connect Onboarding flow.

> **Information**
> 
> Currently, Stripe doesn't support updating the country of the account after the account has been created.

The account data is returned after each create and update Stripe Account API call, so there is no need for separate fetch API call in these cases.

### Handling verification status – Stripe Account Links

After the Connect account exists, the user needs to complete the onboarding. The user can also update the information related to their Stripe Connect account by refilling the Stripe Connect Onboarding flow. This might be necessary if there are new requirements in the user's existing account, or if they want to for example update their contact information.

Stripe Account Links are a mechanism for enabling your providers to access Stripe Connect Onboarding UI. You need to [create an account link](https://www.sharetribe.com/api-reference/marketplace.html#create-stripe-account-link) and provide the return URLs for success and failure cases. The response value is a URL where you can then redirect the provider to complete their Stripe Connect Onboarding. Do note that the Account Link has an expiration time, so you should fetch the link only when the user indicates they want to fill out the details.

When creating the account link, you need to specify the type of the account link. You can also pass an optional *collectionOptions* parameter to define which requirements you want to collect at that point. You can determine that you want your providers to fill out

- only the information that is currently required for this account (*fields: currently_due*)
- or also information that might become required for this account later if it reaches certain thresholds (*fields: eventually_due*)
- and optionally information that is not yet required for any accounts, but will become required in the future (*future_requirements: include*)

See the available values for these options in Stripe's own documentation:

- [Account link types](https://docs.stripe.com/api