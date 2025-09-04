---
title: "Getting started with Sharetribe Web Template"
source: "provided"
extracted_date: "2025-01-09"
summary: "Learn how to install Sharetribe Web Template to your local development environment."
---

- [Setup a development environment](#setup-a-development-environment)
- [Mandatory Integrations](#mandatory-integrations)
- [Add Environment Variables](#add-environment-variables)
- [Start the server](#start-the-server)
- [Summary](#summary)

# Getting started with Sharetribe Web Template

Learn how to install Sharetribe Web Template to your local development environment.

**Required skills:** basic command line, text editing

The Sharetribe Web Template is a marketplace web application built on top of the [Marketplace API](/docs/introduction/introducing-sharetribe-developer-platform/#the-marketplace-api). While you can create a marketplace purely using just the API, it requires a significant amount of effort (both money and time) and we recommened using the template as a starting point for customizations.

The Sharetribe Web Template is built with [React](https://reactjs.org/), [Redux](https://redux.js.org/), and [CSS Modules](https://github.com/css-modules/css-modules). It also contains a small [Node.js](https://nodejs.org/en/) server, which provides server-side rendering (SSR) for the deployed site.

The purpose of this guide is to clone and configure the Sharetribe Web Template to your local development environment - and then get it up and running. This guide also helps you to create accounts to Stripe and Mapbox. Those services are needed to run the Sharetribe Web Template app.

> **Information**
> 
> We recommend that you take the steps for this guide in your **Sharetribe Console dev environment**.
> 
> **[Read more about Sharetribe environments](/docs/concepts/sharetribe-environments/).**

## Setup a development environment

### Prerequisities

To get Sharetribe Web Template up and running, you will need to download and install some basic development tooling:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/)
- [Yarn](https://classic.yarnpkg.com/en/docs/install)

### Install the Sharetribe Web Template App locally

1. Open Terminal

2. [Clone](https://help.github.com/en/github/creating-cloning-and-archiving-repositories/cloning-a-repository) the Sharetribe Web Template repository:

```bash
git clone https://github.com/sharetribe/web-template.git
```

3. Go to the cloned directory:

```bash
cd web-template/
```

4. Install dependency libraries:

```bash
yarn install
```

### Check that you have the correct transaction processes in your environment

If you have created your marketplace environment prior to the 25th of April 2023, and you are using the Sharetribe Web Template, it is good to note that there are two new transaction processes the template uses, and those processes may not be in your Sharetribe marketplace by default. You can find the transaction processes in [/ext/transaction-processes/](https://github.com/sharetribe/web-template/tree/main/ext/transaction-processes) in the repository.

To use the template, you will need to have the transaction processes in your Sharetribe environment. [Follow these steps](https://github.com/sharetribe/web-template#take-the-new-beta-processes-into-use) to create both processes in your environment through Sharetribe CLI.

## Mandatory Integrations

The Sharetribe Web Template has 3 mandatory integrations that you need to configure before the app is fully functional. The app obviously needs to discuss with Sharetribe Marketplace API, but the client app also makes direct calls to [Stripe](https://stripe.com/en-fi). Sharetribe uses Stripe as a payment processor, and Sharetribe Web Template saves sensitive payment information directly to it.

The third default integration is to a map provider. [Mapbox](https://www.mapbox.com) provides location search (geocoding) and maps for the web app.

The Sharetribe Web Template just needs 4 environment variables to make these integrations work.

- **`REACT_APP_SHARETRIBE_SDK_CLIENT_ID`**
- **`SHARETRIBE_SDK_CLIENT_SECRET`**
- **`REACT_APP_STRIPE_PUBLISHABLE_KEY`**
- **`REACT_APP_MAPBOX_ACCESS_TOKEN`**

### Sharetribe client ID and client secret

To use the Marketplace API, you will need a **client ID**. You can [sign up for your free Sharetribe account here](https://console.sharetribe.com/new).

When you get access, you will be able to log into Sharetribe Console and check the client ID.
Sharetribe Console: *Build > Advanced > Applications*

In addition, Sharetribe Web Template uses transaction processes that include privileged transitions. This makes it possible to customize pricing on the Node server that's included in the template. The **client secret** is needed to make this secure call from the template's own server to Sharetribe API.

### Stripe keys

Both Sharetribe API and your client app need to be able to discuss with Stripe API. Stripe has two different keys:

- *Secret key* for server-side requests
- *Publishable key* for calls from web browser

Sharetribe API uses the Stripe secret key to make payment-related requests when a transaction moves forward. The client app needs to use the Stripe publishable key to run the `stripe.js` script. The script has two main functions: it has fraud detection built in, and it is also used to save sensitive information directly to Stripe. For instance, a customer's credit card number is saved directly to Stripe.

#### 1. Create and confirm your free Stripe account

Follow the steps outlined in [this article](https://www.sharetribe.com/help/en/articles/8413086-how-to-set-up-stripe-for-payments-on-your-marketplace?location=conversation#h_7a65415eaa) to get started with Stripe and set up your account.

#### 2. Enable Stripe Connect in your platform

Sharetribe uses the [Stripe Connect](https://stripe.com/docs/connect) features with [Custom accounts](https://stripe.com/docs/connect/accounts#custom-accounts).

[Stripe Connect](https://stripe.com/docs/connect) is used to route payments between customers, providers (sellers), and the marketplace, which can take a commission from transactions.

[Stripe Custom accounts](https://stripe.com/docs/connect/accounts#custom-accounts) are created to hold the provider's account information (e.g. payout details) on Stripe's side. A Custom Stripe account is almost completely invisible to the account holder, but marketplace operators see the accounts on their Stripe dashboard.

**United States**
If you're based in The United States, Stripe will need to review your platform account before you get access. See [this article](https://docs.google.com/document/d/1AdGRFAxQkc_g9UP-IUtwAwo5eOAvGGOw2md-LjybR60/edit#heading=h.iln8r84fkuja) to learn how to apply for Stripe Connect review.

**Other countries**
If you are in any other country, follow these instructions to enable Stripe Connect:

1. Click the **Connect** top menu item, and then click the **Get Started** button.

2. Once a modal opens, select **Platform or marketplace** and click **Continue**.

3. Now when you click the gear icon on the top bar to go to **Settings** at https://dashboard.stripe.com/settings, you will see a new **Connect** section. Click **Settings** in that section.

4. Make sure that **Custom** is enabled in the **Account types** section

Great! You now have to get your API keys and input them into your marketplace.

#### 3. Get your API keys from Stripe and add them to your Sharetribe marketplace

- Click the *Developers* top menu item and go to *Developers* → *API Keys*.
- In the section *"Standard keys"* you will see two keys:
  - Publishable key
  - Secret key

The publishable key (with prefix **pk**) is one used in frontend application (e.g. Sharetribe Web Template) and secret key (with prefix **sk**) is the one you need to add to Console. If you want to use test data make sure the value of the key is eg. **pk_test