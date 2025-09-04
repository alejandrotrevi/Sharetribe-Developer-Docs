```markdown
---
title: "Login as user"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2024-06-17"
summary: "This article provides guidance on how to use the 'Login as User' feature."
---

- [How the Login as user feature works](#how-the-login-as-user-feature-works)
- [Troubleshooting](#troubleshooting)

# Login as user
This article provides guidance on how to use the "Login as User" feature. This functionality allows an operator to log into their marketplace as one of the marketplace users.

## Table of Contents
- [How the Login as user feature works](#how-the-login-as-user-feature-works)
- [Troubleshooting](#troubleshooting)
  - [Authentication fails with message: Failed to authorize as a user, error: <error message>](#authentication-fails-with-message-failed-to-authorize-as-a-user-error-error-message)
  - [Authentication fails with message: Mismatch between redirect_uri parameter value and Marketplace URL](#authentication-fails-with-message-mismatch-between-redirect_uri-parameter-value-and-marketplace-url)
  - [Authentication fails with message: Unable to authenticate as a user](#authentication-fails-with-message-unable-to-authenticate-as-a-user)
  - [Login session drops unexpectedly](#login-session-drops-unexpectedly)

## How the Login as user feature works
The *Login as user* feature allows marketplace operators to log into their marketplace as a specific user of the marketplace. This helps operators to experience their marketplace as their users do and to find out what is wrong when their users are reporting problems. The feature also comes in handy when a marketplace user asks for help with managing their data and listings.

In both Test and Dev environments, you get full access to user profiles and actions with this feature. However, when logged in as a user in a Live environment, it is not possible to modify Stripe account details, send messages, or initiate or transition transactions.

![Authentication Flow](https://your-authentication-flow-image-url.com)

## Troubleshooting
Having trouble enabling the Login as user feature? Check that you have the following in order.

### Authentication fails with message: Failed to authorize as a user, error: <error message>
Double check that the **REACT_APP_MARKETPLACE_ROOT_URL** environment variable of your marketplace website matches the Marketplace URL you have configured in Console.

### Authentication fails with message: Mismatch between redirect_uri parameter value and Marketplace URL
Double check that the **REACT_APP_MARKETPLACE_ROOT_URL** environment variable of your marketplace website matches the Marketplace URL you have configured in Console.

### Authentication fails with message: Unable to authenticate as a user
Have you updated the SDK to the latest version?

### Login session drops unexpectedly
The access token obtained with the Login as user authentication flow is valid only for 30 minutes. If you could not finish what you had in mind during that time you can always log in as the user again.

## Additional Information
- Ensure that the **REACT_APP_MARKETPLACE_ROOT_URL** value configured in your marketplace website matches the marketplace URL configured in Console.
- When developing Sharetribe Web Template locally while testing this feature, you need to set the Marketplace URL as **localhost:4000** and use **yarn run dev-server** so that both your client and server run on the same port.
```