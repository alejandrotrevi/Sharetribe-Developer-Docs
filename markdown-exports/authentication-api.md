```markdown
---
title: "Authentication API"
source: "provided"
extracted_date: "2023-10-24"
summary: "Description of the Sharetribe Authentication API and how the Marketplace API and Integration API applications use it."
---

- [Overview](#overview)
- [Table of Contents](#table-of-contents)
- [Authentication API](#authentication-api)
- [Access Tokens](#access-tokens)
- [Grant Types](#grant-types)

# Overview
The Authentication API is crucial for both the [Marketplace API](https://www.sharetribe.com/docs/concepts/marketplace-api-integration-api/) and the Integration API applications, requiring valid access tokens for API requests. Access tokens are obtained from the Authentication API.

As a general rule:
- Applications that access the Marketplace API authenticate end users via their username and password.
- Integration API applications authenticate using their own credentials.

To access Sharetribe APIs, you need to create an **Application** in [Console](https://console.sharetribe.com/advanced/applications). Each Application has a **client ID**. Applications accessing the Integration API also have a corresponding **client secret**.

## Authentication API
Both the Marketplace API and the Integration API require valid *access tokens* to be passed in every API request.

## Table of Contents
- [Authentication API](#authentication-api)

## Access Tokens
The Authentication API has its main endpoint for [issuing tokens](https://www.sharetribe.com/api-reference/authentication.html#issuing-tokens). Let's look at the different access tokens available:

### Grant Types
Applications request access tokens using several different **grant types**:
- **client_credentials**: 
  - Used by both Marketplace and Integration API applications.
  - Marketplace API applications require just the client ID and grant anonymous access tokens.
  - Integration API applications require both the client ID and client secret, providing full access and a refresh token.

- **password**: 
  - Used only by Marketplace API applications for authenticating end users via username and password.

- **token_exchange**: 
  - Used by Marketplace API to create a trusted context for privileged transitions, utilizing both client ID and secret, and a valid user access token.

- **refresh_token**: 
  - Used by both APIs to obtain a fresh access token provided you have a client ID and valid refresh token.

All access tokens are short-lived (valid for only a certain number of minutes). Implementations are advised to use the refresh_token grant to minimize the risk of long-term secrets being exposed.

## Important Notes
- The **client secret** must be kept safe and secure. Never expose it publicly (e.g., in your website's HTML or JavaScript code).
- The easiest way to interact with both APIs is to use our [SDKs](https://www.sharetribe.com/docs/concepts/js-sdk/), which handle most complexities regarding authentication.

For detailed information, see the [Authentication API reference documentation](https://www.sharetribe.com/api-reference/authentication.html).
```