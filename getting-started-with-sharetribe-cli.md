```markdown
---
title: "Getting Started with Sharetribe CLI"
description: "This tutorial shows you how to get started with the Sharetribe CLI. You will learn how to log in with your API key, how to use the help command and other basic commands."
last_updated: "2023-10-24"
---

- [Install Sharetribe CLI](#install-sharetribe-cli)
- [Help](#help)
- [Get an API Key](#get-an-api-key)
- [Log In](#log-in)
- [List Processes and Process Versions](#list-processes-and-process-versions)
- [Summary](#summary)
- [Troubleshooting](#troubleshooting)
  - [flex-cli: command not found](#flex-cli-command-not-found)
    - [On Windows](#on-windows)
    - [On Mac](#on-mac)

# Getting Started with Sharetribe CLI

This tutorial shows you how to get started with the Sharetribe CLI. You will learn how to log in with your API key, how to use the help command and other basic commands.

## Required skills:
- basic command line
- text editing

## Install Sharetribe CLI
Sharetribe CLI is distributed via [npmjs](https://www.npmjs.com/package/flex-cli). To install packages from npmjs, you will need to download and install the Node.js development environment:

- [Node.js](https://nodejs.org/)
- [Yarn](https://classic.yarnpkg.com/en/docs/install)

When you have installed Node.js and Yarn, type the following command to install Sharetribe CLI:

```bash
yarn global add flex-cli
```

**NOTE:** If you are working with Yarn Modern, use this command instead:

```bash
yarn dlx add flex-cli
```

To verify that Sharetribe CLI was successfully installed, run:

```bash
flex-cli
```

This command should show you the CLI version and list available commands.

Didn't work? Have a look at the [Troubleshooting](#troubleshooting).

## Help
`flex-cli help` is the command to see the list of available commands:

```bash
flex-cli help
```

In order to see subcommand help, pass the command as an argument to the `flex-cli help` command. For example, let's see help for the command `login`.

```bash
flex-cli help login
```

## Get an API Key
To log in you need to have a personal API key.

To get an API key, log in to Console then click your email address in the bottom left of the sidebar and click [Manage API keys](https://console.sharetribe.com/api-keys).

## Log In
After you've received [your API key](#get-an-api-key), you can log in with the `flex-cli login` command. First, let's see help for the login command:

```bash
flex-cli help login
```

You can see that the command does not require any additional options. So let's run it:

```bash
flex-cli login
```

The command will prompt you for your API key.

After successful log in, you will be greeted by your admin email address.

Once logged in, you can work with any marketplace that you have been granted access to.

## List Processes and Process Versions
Now that you have successfully logged in and know how to use the `help` command, let's use CLI to list processes and process versions in your marketplace.

The command to list processes is `process list`. Let's see the help first:

```bash
flex-cli help process list
```

As you can see, the command requires `MARKETPLACE_ID` option. You can use either the long form `--marketplace <marketplace id here>` or short form `-m <marketplace id here>`. You can find the Marketplace ID from [Sharetribe Console](https://console.sharetribe.com/) on the [Build > General](https://console.sharetribe.com/general) page. Optionally the command takes `--process PROCESS NAME` parameter to get detailed information about a single process.

Let's list all the processes:

```bash
flex-cli process list -m my-marketplace-dev
```

This command shows you a list of transaction processes in your marketplace.

## Summary
In this tutorial, we installed Sharetribe CLI, logged in using an API key, and tried some example commands. In addition, we familiarized ourselves with the `help` command that is the main source of documentation for the Sharetribe CLI. 

Now that we know how to list processes, the next step is to [make a small change to the existing process](https://console.sharetribe.com/docs/how-to/edit-transaction-process-with-sharetribe-cli/).

## Troubleshooting
### flex-cli: command not found
If you're seeing `flex-cli: command not found` error and you installed Sharetribe CLI with Yarn, you need to add [Yarn global bin path to the PATH environment variable](https://classic.yarnpkg.com/en/docs/cli/global/#adding-the-install-location-to-your-path).

#### On Windows
1. Run `yarn global bin` to see the global bin path
2. Add it to PATH environment variable
3. Restart command line

For a step-by-step guide with screenshots, have a look at this blog post: [‘yarn global add’ command does not work on Windows](https://sung.codes/blog/2017/12/30/yarn-global-add-command-not-work-windows/).

#### On Mac
1. Open your shell configuration file. If you are using *zsh*, it will be `.zshrc`.
2. Add `export PATH="$(yarn global bin):$PATH"` to the config file.
3. Restart terminal.
```