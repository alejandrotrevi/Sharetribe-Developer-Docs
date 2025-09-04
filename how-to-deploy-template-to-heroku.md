```markdown
---
title: "Deploy to Heroku"
source: "provided"
extracted_date: "2023-10-24"
summary: "This guide describes how to deploy the Sharetribe Web Template to Heroku."
---

- [Overview](#overview)
- [Deploying the Template to Heroku](#deploying-the-template-to-heroku)
- [Set Up Domains and Certificates](#set-up-domains-and-certificates)
- [Heroku Logs](#heroku-logs)
- [Troubleshooting Heroku](#troubleshooting-heroku)

# Overview
This guide provides a practical demonstration of how to deploy the Sharetribe Web Template to Heroku. Heroku is one of the most popular cloud hosting service providers, and because of that, there is a lot of information online that can help you solve and debug potential problems with your deployment. Unlike [Render](https://www.render.com), Heroku does not provide a free tier.

Before deploying your marketplace to Heroku, you need to create three accounts:
- [Heroku](https://www.heroku.com/)
- [Stripe](https://stripe.com/)
- [Mapbox](https://www.mapbox.com/)

## Deploying the Template to Heroku
**Step 1: Create a Heroku account**  
Go to [Heroku](https://www.heroku.com/pricing) and create a new account if you do not have one.

**Step 2: Create a new app**  
Create a new app in the Heroku dashboard.

![Create new app button in Heroku](/docs/static/bcbc7510957248e11891e60bb4283e68/53fb6/heroku-create-new-app.png)

**Step 3: Change the environment variables**  
In Heroku, you can configure environment variables from the [Heroku dashboard](https://devcenter.heroku.com/articles/config-vars#using-the-heroku-dashboard). Go to the Settings page of your new app and reveal Config Vars:

Then add the following environment variables as Config Vars:
- `REACT_APP_SHARETRIBE_SDK_CLIENT_ID`: Sharetribe client ID.
- `SHARETRIBE_SDK_CLIENT_SECRET`: Sharetribe client secret.
- `REACT_APP_STRIPE_PUBLISHABLE_KEY`: Stripe publishable API key for generating tokens.
- `REACT_APP_MAPBOX_ACCESS_TOKEN`: If you are using Mapbox.
- `REACT_APP_MARKETPLACE_ROOT_URL`: Canonical root URL of the marketplace.
- `REACT_APP_MARKETPLACE_NAME`: Marketplace name.
- `NODE_ENV`: Defines whether the application is run in production or development mode.
- `REACT_APP_ENV`: More fine-grained env definition than `NODE_ENV`.
- `REACT_APP_SHARETRIBE_USING_SSL`: Redirect HTTP to HTTPS? Use value: true
- `SERVER_SHARETRIBE_TRUST_PROXY`: Set this when running the app behind a reverse proxy. Use value: true
- `REACT_APP_CSP`: Content Security Policy (CSP). Recommended value is block.

If you change these values later on, *you need to deploy the app again*. Environment variables are baked into the static build files of the web app.

**Step 4: Add a Node.js buildpack**  
Go to the Settings page of your new app and add the official buildpack: *heroku/nodejs*

![Add buildpack](/docs/static/35242bc068759a18b5a17b759a5bdab8/fd73d/heroku-add-buildpack.png)

**Step 5: Connect the Heroku app to Github**  
Go to the Deploy page of your new app and [connect the app with Github](https://devcenter.heroku.com/articles/github-integration#enabling-github-integration).

![Heroku: Connect the app with Github repository](/docs/static/240068b95ffe240e7ab04ff14cffe19b/53fb6/heroku-connect-to-github.png)

If everything works, your app should be available at a URL that looks a bit like this: `HTTPS://<your-app-name>.herokuapp.com`

## Set Up Domains and Certificates
Heroku manages SSL certificates automatically for new applications. You can change your domain and SSH settings in the *Settings tab*.

Read more from Heroku docs:
- [Custom Domain Names for Apps](https://devcenter.heroku.com/articles/custom-domains)
- [Automated Certificate Management](https://devcenter.heroku.com/articles/automated-certificate-management)

![Heroku settings](/docs/static/bacccf10a61d849b245160a88588f603/53fb6/heroku-domains.png)

## Heroku Logs
You can find your application's logs by clicking the button *"More"* in the upper right corner and selecting *"View logs"* from the dropdown. Logs can be useful if there are problems when deploying the app.

![Heroku logs](/docs/static/d23ca6ec7880feec1d28a6c1e64263b8/53fb6/heroku-logs.png)

## Troubleshooting Heroku
By default, Heroku will use the latest Long-Term-Support (LTS) version of Node.js. You might want to specify that your dev and production environments use the same Node version as your local machine when you run `yarn run dev-server`.

This can be done by adding an `engines` section to the `package.json`. Read more from Heroku's [Node.js Support guide](https://devcenter.heroku.com/articles/nodejs-support#specifying-a-node-js-version).

You should also check that the *environment variables* in your local environment match with *Config Vars* in Heroku app settings.
```