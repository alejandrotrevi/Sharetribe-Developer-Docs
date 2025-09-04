```markdown
---
title: "Deploy to production"
source: "provided"
extracted_date: "2024-08-23"
summary: "This article describes what to take into account when you deploy the Sharetribe Web Template to production."
---

- [What is a production environment?](#what-is-a-production-environment)
- [Where to host your application?](#where-to-host-your-application)
- [Deploying to production](#deploying-to-production)
- [Environment variables](#environment-variables)
- [Building the app](#building-the-app)
- [Start the server](#start-the-server)

# Deploy to production
This article describes what to take into account when you deploy the Sharetribe Web Template to production.

## What is a production environment?
A production environment hosts and executes the code that runs your live marketplace and serves it to the public internet. The version of your marketplace intended for real-life use with real money runs in the production environment. Typically, alongside your production environment, you will also host another version of your client environment: a test environment.

Having several environments is a common practice in software development. The test environment is most often a clone of the production environment intended for testing new features before being deployed to production. 

Sharetribe offers [three different environment types](https://www.sharetribe.com/docs/concepts/sharetribe-environments/#environment-types) – Live, Test, and Dev. You should connect your client application with the corresponding marketplace environment.

More specifically, the workflow recommended with Sharetribe is that you have three deployments of your client application:
- **Production deployment**: connected to your Live environment and running real transactions.
- **Test deployment**: connected to your Test environment and intended for previewing no-code changes.
- **Dev deployment**: connected to your Dev environment and intended for testing and previewing code-level changes.

We recommend that you keep your production and test deployments identical, so that operators can preview their no-code changes reliably. Read more: [Sharetribe environments](https://www.sharetribe.com/docs/concepts/sharetribe-environments/#workflow-between-the-three-environments).

## Where to host your application?
There are many hosting providers to choose from when considering where to host your marketplace. Our official recommendation is to host your marketplace on Heroku or Render for a hassle-free installation. 

When choosing a hosting provider, you should not only consider the cheapest option. Scalability, tools, service-level agreements, and available computing resources are examples of factors that should weigh in when choosing a hosting provider.

As of the time of writing, [Render](https://www.render.com) and [Fly.io](https://fly.io) continue to provide a free tier, which you can use to host a test application.

Other alternatives you can look into include [AWS](https://aws.amazon.com/), [Google Cloud](https://cloud.google.com/), [Digital Ocean](https://www.digitalocean.com/), and [Microsoft Azure](https://azure.microsoft.com/).

If your deployment solution requires using containers, you can refer to our guide on [running the template in a Docker container](https://www.sharetribe.com/docs/template/run-template-with-docker/).

## Deploying to production
Deploying your marketplace to production is a three-step process:
1. [Set environment variables](#environment-variables)
2. [Build the app](#building-the-app)
3. [Run the node server](#start-the-server)

### Environment variables
If you are transitioning from a Sharetribe hosted marketplace to a self-hosted marketplace and you are using Social Logins, you will need to enable the logins through [environment variables](https://www.sharetribe.com/docs/template/template-env/). 

For a full list of possible environment variables, see the [Environment configuration variables](https://www.sharetribe.com/docs/template/template-env/) reference for more information. To deploy your marketplace, you need to add at least the following variables:
- **`NODE_ENV`**: Use the value 'production'.
- **`PORT`**: Set a port if the production environment does not set one by default.
- **`REACT_APP_SHARETRIBE_SDK_CLIENT_ID`**: Your client ID.
- **`SHARETRIBE_SDK_CLIENT_SECRET`**: Your client secret.
- **`REACT_APP_STRIPE_PUBLISHABLE_KEY`**: Stripe publishable API key.
- **`REACT_APP_MARKETPLACE_ROOT_URL`**: The root URL of the marketplace.
- **`REACT_APP_MARKETPLACE_NAME`**: The Marketplace name in self-hosted marketplaces.
- **`REACT_APP_MAPBOX_ACCESS_TOKEN`**: The Mapbox access token.

### Building the app
Running the following command builds the app for production to the build folder. It correctly bundles React in production mode and optimizes the build for the best performance.
```bash
yarn build
```
After this, your app is ready to be deployed.

### Start the server
To start the server, you need to run:
```bash
yarn start
```

### Conclusion
Can't find what you're looking for? Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```