```markdown
---
title: "User Extended Data"
source: "provided"
extracted_date: "2025-06-11"
summary: "User extended data allows you to distinguish between different types of users, as well as share sensitive information to a transaction counterparty."
---

- [Introduction](#introduction)
- [Viewing and Modifying User Extended Data](#viewing-and-modifying-user-extended-data)
- [Types of Users](#types-of-users)
- [Revealing Information Within the Transaction](#revealing-information-within-the-transaction)
- [User Filtering in Integration API](#user-filtering-in-integration-api)

# Introduction
User extended data allows you to distinguish between different types of users, such as customers and providers or regular and verified providers, as well as share sensitive information with a transaction counterparty.

## Last updated
June 11, 2025

## Table of Contents
- [Viewing and modifying user extended data](#viewing-and-modifying-user-extended-data)
- [Types of users](#types-of-users)
- [Revealing information within the transaction](#revealing-information-within-the-transaction)
- [User filtering in Integration API](#user-filtering-in-integration-api)

# Viewing and Modifying User Extended Data
User public and metadata are visible through the [users/show](https://www.sharetribe.com/api-reference/marketplace.html#show-user) endpoint in Marketplace API. In addition, authenticated users can view their own extended data through the [current_user/show](https://www.sharetribe.com/api-reference/marketplace.html#show-current-user) endpoint. Operators can see all user extended data through the [user retrieval endpoints](https://www.sharetribe.com/api-reference/integration.html#users) in Integration API.

Authenticated users are able to modify their own public, protected, and private extended data through the [current user creation and update endpoints](https://www.sharetribe.com/api-reference/marketplace.html#current-user) in Marketplace API. Operators can modify all user extended data using the [users/update_profile](https://www.sharetribe.com/api-reference/integration.html#update-user-profile) in Integration API.

# Types of Users
By default, Sharetribe marketplace users can act as both providers and customers. However, you may want to limit those capabilities. For instance, you may want only verified marketplace users to create listings or to establish a three-way marketplace where a single type of participant can assume multiple roles. 

Starting from release [v8.5.0](https://github.com/sharetribe/web-template/releases/tag/v8.5.0), operators can differentiate users' experiences based on user type in Console, and the Sharetribe Web Template will display selected user interface elements differently based on those configurations.

You can also implement different tiers of providers, where a regular (non-paid) provider is allowed to publish one listing and a premium (paid) provider can publish three.

# Revealing Information Within the Transaction
User extended data can also be used for sensitive information that transaction participants may want to reveal only to the other participant. This can include contact information such as phone numbers or access instructions to a listing's goods or services.

As part of your marketplace's transaction process, you can have an action that reveals the user's protected data in the transaction. The revealed protected data becomes available in the transaction's protected data and can be shown to either of the transaction participants.

# User Filtering in Integration API
The Marketplace API does not have an endpoint for querying users. However, when building an integration or creating analytics, you can use the [user query endpoint in Integration API](https://www.sharetribe.com/api-reference/integration.html#query-users).

To facilitate user querying, it's possible to filter users by extended data when calling the endpoint. That way, you can segment your users by important attributes for more fine-grained processing. To filter users by extended data, create a [search schema](https://www.sharetribe.com/docs/how-to/manage-search-schemas-with-sharetribe-cli/) for the user profile.

## Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at the [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```