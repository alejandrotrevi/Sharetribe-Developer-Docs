```markdown
---
title: "Enable Google login"
source: "provided"
extracted_date: "2023-10-24"
summary: "In this how-to guide we'll take a look at the process of setting up Google login for your marketplace. It will allow your users to sign and log in by using their Google accounts."
---

- [Introduction](#introduction)
- [Enabling Google login](#enabling-google-login)
  - [Configure the Google Sign-In Project](#configure-the-google-sign-in-project)
  - [Configure an identity provider client in Console](#configure-an-identity-provider-client-in-console)
  - [Configure Sharetribe Web Template](#configure-sharetribe-web-template)

# Introduction
In this how-to guide we'll take a look at the process of setting up Google login for your marketplace. It will allow your users to sign and log in by using their Google accounts.

## Enabling Google login
Enabling Google login consists of three main steps:
1. **Create a Google Sign-In Project.** Google Sign-In Project is what connects your marketplace to Google and lets Google know that users from your marketplace are allowed to authenticate themselves using the Google Sign-In.
   
2. **Create an identity provider client in Sharetribe Console.** The Identity provider (IdP) client lets Sharetribe know that the users of your marketplace are allowed to use the Google Sign-In Project you created to log into your marketplace.
   
3. **Configure Sharetribe Web Template.** A few attributes from the Sign-In Project will need to be configured to Sharetribe Web Template so that it can perform the login flow via Google.

### Configure the Google Sign-In Project
The first thing to do is to create a Google Sign-In project for your marketplace by following [these steps in the Sharetribe help center](https://www.sharetribe.com/help/en/articles/9174430-how-to-enable-google-login#h_1e6314ef08).

### Configure an identity provider client in Console
Now that your Google Sign-In project is all set up, you will need to configure a corresponding *identity provider client* for your marketplace. This will tell Sharetribe that your users will be allowed to log into your marketplace using the Google Sign-In you just created. The information stored in an IdP client is used to verify a token obtained from Google when a user logs in.

An identity provider client can be configured with the following steps:
1. Go to [Social logins & SSO in Console](https://console.sharetribe.com/advanced/social-logins-and-sso).
2. Under *Identity provider clients* click "+ Add new".
3. Set "Client name". This can be anything you choose, for example, "Google login".
4. Under "Select identity provider", pick "Google".
5. Set the *Client ID*. This value is the Client ID from your Google Sign-In project. You can see the value under *Credentials > OAuth 2.0 client IDs*. Make sure you have the project you just created selected from the top bar in Google developer console. In case you have multiple clients configured in Google Sign-In, use the client ID of your *Web application* client here. See step 6 for more information.
6. If you have more than one client configured in your Google Sign-In project, add the additional client IDs to the same identity provider client under "Trusted client IDs" by clicking "+ Add new trusted client". In case you are using two distinct Google Sign-In projects, configure those as distinct clients in Console but always bundle all the client IDs of a single project into one identity provider client in Sharetribe Console.
7. Click "Add client" and your identity provider client is ready.

### Configure Sharetribe Web Template
Last step to enabling Google login is to configure your Sharetribe Web Template with the values that you used to add an identity provider client in Console. Add the following environment variables to the template:
- **`REACT_APP_GOOGLE_CLIENT_ID`**: The Client ID of your Google Sign-In. Corresponds to *client ID* of the identity provider client in Console.
- **`GOOGLE_CLIENT_SECRET`**: The Client Secret of your Google Sign-In.

For more information on the template environment variables, see the [Template environment variables](https://www.sharetribe.com/docs/template/template-env/) article.

That is it. Setting these environment variables will make Sharetribe Web Template render the Google login button in signup and login forms.

## Can't find what you’re looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```