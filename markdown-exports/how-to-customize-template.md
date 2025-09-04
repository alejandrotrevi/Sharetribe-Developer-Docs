```markdown
---
title: "Customizing the Template"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2023-10-24"
summary: "This article helps you set up the development environment and outlines the best practices you should follow while developing on the Sharetribe Web Template."
---

- [Prerequisites](#prerequisites)
- [Create a marketplace environment](#create-a-marketplace-environment)
- [Check that you have the correct transaction processes in your environment](#check-that-you-have-the-correct-transaction-processes-in-your-environment)
- [Getting started with the template](#getting-started-with-the-template)
- [Pull in the latest upstream changes](#pull-in-the-latest-upstream-changes)
- [Installing dependencies](#installing-dependecies)
- [Configuring environment variables](#configuring-environment-variables)
- [Development](#development)
- [Tests](#tests)
- [Further reading](#further-reading)

# Customizing the Template

This article helps you set up the development environment and outlines the best practices you should follow while developing on the Sharetribe Web Template.

## Prerequisites

When you start a new project, you should create a new Git repository for your project and add the template repository as a remote repository. That allows you to update your code with the latest changes from the upstream repository. See how to set up a remote repository in the [tutorial](https://www.sharetribe.com/docs/tutorial/introduction/#prerequisites).

## Create a marketplace environment

The Sharetribe Web Template is a React application built on top of the [Marketplace API](https://www.sharetribe.com/docs/concepts/marketplace-api-integration-api/). While you can create a marketplace client application from scratch using just the API, it requires a lot of effort and we recommend that you use a template as a starting point for customizations.

To use the Marketplace API, you will need a client ID. You can obtain one from your [Sharetribe Console > Advanced > Applications](https://console.sharetribe.com/advanced/applications). If you do not have a Sharetribe marketplace yet, you can create one at [the Sharetribe website](https://console.sharetribe.com/new).

## Check that you have the correct transaction processes in your environment

If you have created your marketplace environment prior to April 25, 2023, and you are using the Sharetribe Web Template, it is good to note that there are two new transaction processes the template uses, and those processes may not be in your Sharetribe marketplace by default. You can find the transaction processes in [the repository](https://github.com/sharetribe/web-template/tree/main/ext/transaction-processes).

To use the template, you will need to have the transaction processes in your Sharetribe environment. [Follow these steps](https://github.com/sharetribe/web-template#take-the-new-beta-processes-into-use) to create both processes in your environment through Sharetribe CLI.

## Getting started with the template

If you are new to Sharetribe or the Sharetribe Web Template, we recommend reading these articles before starting to work on development:

- [Introducing Sharetribe](https://www.sharetribe.com/docs/introduction/introducing-sharetribe-developer-platform/)
- [What development skills are needed?](https://www.sharetribe.com/docs/introduction/development-skills/)
- [Getting started](https://www.sharetribe.com/docs/introduction/getting-started-with-web-template/)

The [tutorial introduction](https://www.sharetribe.com/docs/tutorial/introduction/#prerequisites) will also walk you through creating a GitHub repository.

## Pull in the latest upstream changes

To update your project with the newest changes from the remote repository, you need to pull these changes from the upstream remote.

> **Information**: Pulling the newest changes from the upstream remote might be hard or impossible, depending on the extent of the changes you have made to the template. The template is a starting point for development rather than something that you should regularly update.

You should follow the [tutorial](https://www.sharetribe.com/docs/tutorial/introduction/) to set up a local development environment and connect it to GitHub.

Run the following commands in a new branch.

1. Create a new branch and switch into that branch:
    ```shell
    git checkout -b updates-from-upstream
    ```

2. Fetch the latest changes from the upstream repository:
    ```shell
    git fetch upstream
    ```

3. Merge the changes to your local branch:
    ```shell
    git merge upstream/main
    ```

4. Fix possible merge conflicts, commit, and push/deploy.

If you have forked the repository instead of setting a remote, see how to [sync a fork](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/syncing-a-fork).

## Installing dependencies

In your project root, install dependency libraries:
```shell
yarn install
```

## Configuring environment variables

You need to configure some environment variables before deploying or running the template locally. You can simply run the following command in the root of your project directory:
```shell
yarn run config
```
This command will create a `.env` file and guide you through setting up the required environment variables.

The `.env` file is the place to add your *local* configuration. It is ignored by Git, so you will have to add the corresponding configuration also to your server environment. When deploying the template to Render or Heroku, you need to configure the environment variables in the hosting platform. See our article on deploying the template to [Render](https://www.sharetribe.com/docs/tutorial/deploy-to-render/#deploy-to-render) for more information.

See the full list of [environment variables](https://www.sharetribe.com/docs/template/template-env/) for more information.

For in-app configurations, see the [src/config directory](https://github.com/sharetribe/web-template/tree/main/src/config).

## Development

To develop the application and to see changes live, start the frontend development server:
```shell
yarn run dev
```

### Extra: troubleshooting

**Known issues**:
- Adding/changing `import`s may not be synced properly with ESLint. You may see an error `Unable to resolve path to module` even though the module exists in the right path. Restarting the server doesn't help. To solve the issue, you need to make a change to the file where the error occurs.

#### Development with the actual Node.js server

The usual way to develop the application is to use the frontend development server (see above). However, in production you likely want to use the server-side rendering (SSR) setup. To develop against the actual server locally, run:
```shell
yarn run dev-server
```
This runs the frontend production build and starts the Express.js server in [server/index.js](https://github.com/sharetribe/web-template/blob/main/server/index.js).

> **Information**: This server does **not** detect changes in the frontend application code. For that you need to rebuild the client bundle by restarting the server manually.

## Tests

To start the test watcher, run:
```shell
yarn test
```
For more information on tests, see the documentation on [how to test the template](https://www.sharetribe.com/docs/template/how-to-test-template/).

## Further reading

There are many things that you should change in the default template and many more that you can change. For more information, check the [template customization checklist](https://www.sharetribe.com/docs/template/customization-checklist/) documentation before publishing your site. Also see the [tutorial](https://www.sharetribe.com/docs/tutorial/introduction/) and other articles in the [Sharetribe Web Template](https://www.sharetribe.com/docs/template/) and [How-to](https://www.sharetribe.com/docs/how-to/) categories.

## Can't find what you're looking for?

Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```