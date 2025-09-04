```markdown
---
title: "Enable Facebook login"
source: "provided"
extracted_date: "2023-10-24"
summary: "In this how-to guide we'll take a look at the process of setting up Facebook login for your marketplace. It will allow your users to sign and log in by using their Facebook accounts."
---

- [Introduction](#introduction)
- [Tutorial](#tutorial)
- [Concepts](#concepts)
- [How-Tos](#how-tos)
  - [Users and Authentication](#users-and-authentication)
  - [Listings](#listings)
  - [Transaction process](#transaction-process)
  - [Payments and Stripe](#payments-and-stripe)
  - [Migrations](#migrations)
  - [Content Management](#content-management)
  - [Emails & notifications](#emails-notifications)
  - [Events](#events)
  - [Search](#search)
- [Sharetribe Web Template](#sharetribe-web-template)
- [Design Toolkit](#design-toolkit)
- [References](#references)

# Enable Facebook login

In this how-to guide we'll take a look at the process of setting up Facebook login for your marketplace. It will allow your users to sign and log in by using their Facebook accounts.

## Table of Contents

- [Configure a Facebook app](#configure-a-facebook-app)
- [Configure an identity provider client in Console](#configure-an-identity-provider-client-in-console)
- [Configure Sharetribe Web Template](#configure-sharetribe-web-template)

## Enabling Facebook login consists of three main steps:

1. **Create a Facebook app**: The Facebook app connects your marketplace to Facebook and let's Facebook know that users from your marketplace are allowed to authenticate themselves using Facebook login.
2. **Create an identity provider client in Sharetribe Console**: The IdP client informs Sharetribe that the users of your marketplace are allowed to use the Facebook app you created to log in to your marketplace.
3. **Configure Sharetribe Web Template**: A few attributes from the Facebook app need to be configured in your Sharetribe Web Template to enable the login flow via Facebook.

## Configure a Facebook app

The first step is to create a Facebook development app for your marketplace by following [these steps in the Sharetribe help center](https://www.sharetribe.com/help/en/articles/9174337-how-to-enable-facebook-login#h_3848596b4d).

## Configure an identity provider client in Console

Now that your Facebook app is set up, you will need to configure a corresponding *identity provider client* for your marketplace. This tells Sharetribe that your users can log into your marketplace using the Facebook app you just created. The information stored in an IdP client is used to verify a token obtained from Facebook during the login process.

### Steps to configure an identity provider client:

1. Go to [Social logins & SSO in Console](https://console.sharetribe.com/advanced/social-logins-and-sso).
2. Under *Identity provider clients*, click "+ Add new".
3. Set "Client name". This can be anything you choose, such as "Facebook login". If you need to create multiple Facebook apps, this will help distinguish between the corresponding IdP clients.
4. Set the *Client ID*. This value is the App ID from your Facebook app, visible under *Settings > Basic* in the Facebook app view.
5. Set the *Client secret*. This value is the App secret in your Facebook app, also visible under *Settings > Basic* in your Facebook app view. (You may need to authenticate to reveal the secret value).
6. Click "Add client" and your identity provider client is ready.

## Configure Sharetribe Web Template

The last step to enabling Facebook login is to configure your Sharetribe Web Template with the values used to add the identity provider client in Console. Add the following environment variables to your template:

- **`REACT_APP_FACEBOOK_APP_ID`**: The App ID of your Facebook app, corresponding to the *client ID* of the identity provider in Console.
- **`FACEBOOK_APP_SECRET`**: The App Secret of your Facebook app, corresponding to the *client secret* of the identity provider in Console.

For more information on template environment variables, see the [Template environment variables](https://www.sharetribe.com/docs/template/template-env/) article.

Setting these environment variables will enable Sharetribe Web Template to render the Facebook login button in signup and login forms.

## Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```