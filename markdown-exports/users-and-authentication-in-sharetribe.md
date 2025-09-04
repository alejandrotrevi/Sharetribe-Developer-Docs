---
title: "Users and authentication in Sharetribe"
source: "https://www.sharetribe.com/docs/concepts/users-and-authentication-in-sharetribe/"
extracted_date: "2025-01-04"
summary: "This article explains how users are managed and how authentication and authorization works in Sharetribe."
---

- [User roles in Sharetribe](#user-roles-in-sharetribe)
  - [Customer](#customer)
  - [Provider](#provider)
  - [Transaction related roles: operator and system](#transaction-related-roles-operator-and-system)
- [User access](#user-access)
  - [Registered marketplace users](#registered-marketplace-users)
  - [Login as user](#login-as-user)
  - [Integration API](#integration-api)
  - [Authenticating to Sharetribe APIs](#authenticating-to-sharetribe-apis)
- [Restricted user states in a Sharetribe marketplace](#restricted-user-states-in-a-sharetribe-marketplace)
  - [User pending approval](#user-pending-approval)
  - [Banned user](#banned-user)
  - [Deleted user](#deleted-user)
- [Authentication in Sharetribe](#authentication-in-sharetribe)
  - [Anonymous access to Marketplace API](#anonymous-access-to-marketplace-api)
  - [User access to Marketplace API](#user-access-to-marketplace-api)
  - [Trusted access to Marketplace API](#trusted-access-to-marketplace-api)
  - [Full access to Integration API](#full-access-to-integration-api)

# Users and authentication in Sharetribe

This article explains how users are managed and how authentication and authorization works in Sharetribe.

Anyone who registers to your Sharetribe marketplace is referred to as a user. In addition, the operator can take certain actions on the marketplace even though they are not technically a user there. Sharetribe marketplaces only facilitate transactions between registered users – in other words, it is not possible for someone to purchase or book a listing without signing up as a user.

## User roles in Sharetribe

Sharetribe has two possible roles for a registered user: customer and provider. All users can be both customers and providers by default. This means that even if a person has created a listing, which means they can be a provider, they can also be a customer on someone else's listing.

If you want to limit certain users to only customers or only providers, you will need to create those limitations in your client application. You can use [extended data](/docs/concepts/extended-data-introduction/) to determine that a user is in a certain group, and then allow a subset of your marketplace functionalities, e.g. listing creation, for a specified group. You can, for example, [define user types in Console](https://www.sharetribe.com/help/en/articles/9117175-what-are-user-types) for this purpose.

### Customer

In a transaction, a **customer** is the user who purchases or books the listing. In marketplaces with payments, the customer is the user who pays the listing price. Customers can save their payment details in Sharetribe.

Customers need to enter a valid email address to sign up to Sharetribe, but other than that customers are not required to enter further information to use the marketplace. When using the Stripe default payment integration, customers can save a payment method, however the payment method information is saved in Stripe and not directly in Sharetribe.

Within a transaction, customers can initiate or transition transactions specified for the customer.

### Provider

When a user creates a listing and someone else books or purchases that listing, the user becomes the **provider** of the transaction in question. In marketplaces with payments, the provider is the transaction party who receives the listing price.

This means that if the payment happens within the marketplace, the provider will need to verify their identity to the payment gateway to adhere to the payment processor's [Know Your Customer requirements](https://en.wikipedia.org/wiki/Know_your_customer).

Within a transaction, providers can initiate or transition transactions specified for the provider.

### Transaction related roles: operator and system

A **marketplace operator** is not a user in the marketplace – they cannot sign in to the marketplace with the same credentials they use to sign in to Sharetribe Console. The operator can, however, take actions on the marketplace through Sharetribe Console or Integration API, when those actions are defined for the operator. Operators cannot participate in the messaging between customer and provider within the transaction.

A transaction transition is performed by the **system** if it is scheduled to happen automatically.

## User access

Sharetribe marketplace listings and user profiles on public marketplaces can be viewed by anyone by default, whether they are registered users or not. If you set your marketplace to private, only registered users can view listings and user profiles.

When a user wants to start a transaction or create a listing, they need to be a registered user on your marketplace. You can also choose to limit additional rights for individual registered users.

### Registered marketplace users

Listings can only be updated by their author, i.e. the registered user who originally created the listing. Operators can create listings for a registered user, and update existing listings.

When user approval is required on the marketplace, operators can individually determine which users can post listings and start transactions. On private marketplaces, user approval affects viewing rights as well. Listing posting rights can also be determined per user.

Beyond that, Sharetribe does not have different levels of user access within the marketplace. Operators who want to create more complex user hierarchies will need to think about the levels of user access they want each custom role to have, and potentially use a custom backend solution to complement Sharetribe default user management. If you are contemplating creating a user hierarchy in your Sharetribe marketplace, contact [Sharetribe Support](mailto:hello@sharetribe.com) and let us know your use case – we're happy to help you figure out a suitable solution!

### Login as user

Sharetribe has a feature through which operators can log in to their marketplace as a registered user and take limited actions on their behalf. When using the Login as user feature in Live environments, operators cannot initiate or transition transactions or modify the user's payout information. However, they can e.g. create and update listings on behalf of the user. The login as user feature can be accessed through the Sharetribe Console, by navigating to a user profile and clicking on the three dots next to the profile image of the user.

### Integration API

Sharetribe Integration API allows trusted secure applications to access all data within a marketplace. It is not accessible for marketplace users with their own sign-in credentials. Instead, Integration API can be used to create server-side integrations to external systems, or to retrieve data for custom marketplace dashboards.

### Authenticating to Sharetribe APIs

Sharetribe marketplace users need to sign up with their email address to create listings and participate in transactions. Alternatively, they can use [social logins](/docs/concepts/social-logins-and-sso/) to sign up, or to login with an email address that already has a user within Sharetribe.

Sharetribe has a separate [Authentication API](/docs/concepts/authentication-api/) that handles authentication to other Sharetribe APIs. Both Marketplace API and Integration API require valid access tokens to be passed in every API request. If you use the [Javascript SDKs](/docs/concepts/js-sdk/) in your marketplace client application, they handle authenticating the user automatically when they enter their credentials.

## Restricted user states in a Sharetribe marketplace

When a user is pending approval, banned, or deleted, they do not have full access to the marketplace functionalities. User state is exposed as a part of the `currentUser` resource.

### User pending approval

You can set your marketplace to require that users be approved before they can participate in the marketplace. You can approve users in Console.

For public marketplaces, this means that users cannot post or modify listings or start transactions before they have been approved. For private marketplaces, users pending approval also cannot view listings or other user profiles.

A user pending approval can edit their own profile, but it will only become visible to other marketplace users once the user has been approved.

### Banned user

Banning a user means removing the user and all of the user's listings from a marketplace due to inappropriate behaviour. The email with which a banned user registered to the marketplace can not be used to create new accounts. The user data is only visible when it is linked to, and even then only ID and banned status are shown.

Operators can ban and unban users through Sharetribe Console, but there is no endpoint in the Sharetribe APIs to ban a user. Unbanning a user does not automatically reinstate the user's deleted listings.

### Deleted user

Deleting a user means completely and irreversibly removing all of the user's personal data. This includes all of the public-facing data like profile and listings as well as the user account information.

Operators can delete