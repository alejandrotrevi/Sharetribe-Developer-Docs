```markdown
---
title: "Start Customizing Your Marketplace"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2023-10-31"
summary: "When you want to start customizing your marketplace, there are some general topics that require your attention. Learn what to take into consideration when starting custom development."
---

- [Table of Contents](#table-of-contents)
- [General Custom Development Guidelines](#general-custom-development-guidelines)
- [Get Familiar with the Sharetribe APIs](#get-familiar-with-the-sharetribe-apis)
- [Get Familiar with Sharetribe Environments](#get-familiar-with-sharetribe-environments)
- [Get Familiar with Hosting Your Application](#get-familiar-with-hosting-your-application)
- [Custom Developing with the Sharetribe Web Template](#custom-developing-with-the-sharetribe-web-template)
- [Custom Developing with Your Own Client Application](#custom-developing-with-your-own-client-application)

# Introduction
When you want to start customizing your marketplace, there are some general topics that require your attention. Learn what to take into consideration when starting custom development.

## Table of Contents
- [General custom development guidelines](#general-custom-development-guidelines)
- [Get familiar with the Sharetribe APIs](#get-familiar-with-the-sharetribe-apis)
- [Get familiar with Sharetribe environments](#get-familiar-with-sharetribe-environments)
- [Get familiar with hosting your application](#get-familiar-with-hosting-your-application)
- [Custom developing with the Sharetribe Web Template](#custom-developing-with-the-sharetribe-web-template)
- [Custom developing with your own client application](#custom-developing-with-your-own-client-application)

## General Custom Development Guidelines
Whichever custom development approach you choose, here are some key points that apply to all custom development.

## Get Familiar with the Sharetribe APIs
Your marketplace basic functions are available through the **Marketplace API**. Your no-code configurations are available through the public **Asset Delivery API**. Capabilities for creating custom integrations and data analytics are available server-side through **Integration API**. Authentication to Marketplace API and Integration API happens through the **Authentication API**. Access to the Sharetribe APIs in the live environment is available in the Extend plan. The Marketplace API, Asset Delivery API, and Integration API are also accessible through JavaScript SDKs.

- [Read more about Marketplace API and Integration API](https://example.com)
- [Read more about Asset Delivery API](https://example.com)
- [Read more about Authentication API](https://example.com)
- [Read more about Sharetribe JavaScript SDKs](https://example.com)
- [Read more about Sharetribe pricing](https://www.sharetribe.com/pricing/)

## Get Familiar with Sharetribe Environments
Your marketplace has three environments – Test, Dev, and (eventually) Live. We recommend that you only do development work in Dev, and keep Test and Live identical in terms of code.

- [Read more about Sharetribe environments](https://example.com)
- [Read more about Sharetribe applications](https://example.com)

## Get Familiar with Hosting Your Application
When you custom develop your marketplace, you also need to host your own application. When starting your custom development, it is enough to host your custom client application for the Dev environment. This makes it possible for several people to test and validate the features you are developing. 

## Custom Developing with the Sharetribe Web Template
If you want to continue developing your marketplace based on the Sharetribe-hosted client app, you can use the Sharetribe Web Template as your starting point. 

- [Read more about the Sharetribe Web Template](https://example.com)

## Custom Developing with Your Own Client Application
You can also create a fully custom client application for your marketplace. The headless architecture of the Sharetribe Developer Platform allows you to build fully custom applications.

### Important Notes
Do note that currently we only have SDKs available for JavaScript. 

- [Read the tutorial](https://example.com)

### Required Server Features
Features requiring a server include:
- Using SSO and identity providers
- Integration API – it provides full access to your marketplace, so it must never be used from a browser environment
- Privileged transitions, including custom pricing
- Logging in as a user from Console

- [Read more about using SSOs with a Sharetribe marketplace](https://example.com)
- [Read more about Integration API security](https://example.com)
- [Read more about privileged transitions](https://example.com)
- [Read more about Login as user](https://example.com)

## Conclusion
For detailed information and more topics, refer to the documentation. Reach out via [email](mailto:hello@sharetribe.com) or consult the [Sharetribe Developer Community](https://www.sharetribe.com/dev-slack).
```