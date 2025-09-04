```markdown
---
title: "Sharetribe Marketplace API and Integration API"
source: "provided"
extracted_date: "2023-10-24"
summary: "Description of the different Sharetribe APIs and how to choose which one to use."
---

- [Introduction](#introduction)
- [When to use the Marketplace API](#when-to-use-the-marketplace-api)
- [When to use the Integration API](#when-to-use-the-integration-api)
- [Authentication](#authentication)

# Sharetribe Marketplace API and Integration API
Description of the different Sharetribe APIs and how to choose which one to use.

## Last updated 
October 24, 2023

## Introduction
Sharetribe provides two different main APIs for interacting with the marketplace and accessing its data: the *Marketplace API* and the *Integration API*. In addition, Sharetribe also has the *Authentication API*, which is used to authenticate and obtain access credentials that are needed to access both the Marketplace API and the Integration API.

## When to use the Marketplace API
The Marketplace API enables all the core interactions that the end users of the marketplace participate in. These include signing up as a new user, managing listings, engaging in transactions, messaging, etc. The Marketplace API is best suited for building user-facing marketplace UI applications, like the marketplace's web site or mobile apps.

With the Marketplace API, the authenticated party is always the end user, and all API calls are made in the name of that authenticated user (with the exception of anonymous public access). Therefore, the Marketplace API allows access to the individual user’s own data and any publicly available data. This allows the Marketplace API to be directly called from a user-controlled device or web browser, which is the case if you are using the open source [template application](https://www.sharetribe.com/docs/introduction/introducing-template/) provided by Sharetribe.

The Marketplace API does not provide access to any data that an individual user should not access, such as other users' private data, transactions, or messages.

The easiest way to access the Marketplace API is to use the [Sharetribe SDK for JavaScript](https://www.sharetribe.com/docs/concepts/js-sdk/#sharetribe-sdk-for-javascript). See also the [Marketplace API reference documentation](https://www.sharetribe.com/api-reference/marketplace.html).

## When to use the Integration API
The Integration API allows trusted secure applications to access the entire marketplace data: all users, listings, transactions, messages, etc. Such trusted applications are for example applications that run in your own backend systems or applications meant to be executed by authorized marketplace operators.

**Warning**: Never expose your Integration API [application](https://www.sharetribe.com/docs/concepts/applications/) credentials to an untrusted device or application, such as an end user’s browser or mobile app.

The Integration API is well suited for building the following types of applications:
- Integrations with other own or third party systems, which can be stand-alone or work in combination with the end-user facing marketplace application and provide advanced features
- Custom reporting
- Custom tooling for operators managing the marketplace

The easiest way to access the Integration API is to use the [Sharetribe Integration SDK for JavaScript](https://www.sharetribe.com/docs/concepts/js-sdk/#sharetribe-integration-sdk-for-javascript). See also the [Integration API reference documentation](https://www.sharetribe.com/api-reference/integration.html).

## Authentication
Both the Marketplace API and the Integration API require valid *access tokens* to be passed in every API request. Applications obtain those access tokens from the Authentication API.

As a general rule, applications that access the Marketplace API do so by authenticating an end user of the marketplace (via the user's username and password), while Integration API applications authenticate using their own credentials.

For more details see the [Authentication API](https://www.sharetribe.com/docs/concepts/authentication-api/) article and the [API reference documentation](https://www.sharetribe.com/api-reference/authentication.html).

## Footer
### Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```