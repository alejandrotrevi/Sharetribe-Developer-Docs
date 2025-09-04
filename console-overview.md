```markdown
---
title: "Understanding Console"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2024-02-07"
summary: "This article will help you understand how to navigate Console."
---

- [Table of Contents](#table-of-contents)
- [Environments](#environments)
- [Sections](#sections)
- [Home](#home)
- [Manage](#manage)
- [Build](#build)
- [Side bar](#side-bar)
- [Action buttons](#action-buttons)

# Understanding Console
This article will help you understand how to navigate Console.

## Last updated 
February 7, 2024

---

## Console Overview
Console is the central control system of your Sharetribe marketplace. In Console, you can change a number of general settings and manage most of the data of your marketplace related to users, listings, transactions, and reviews.

When you create a Sharetribe account, you also create an organization within Sharetribe’s system. You can invite multiple people to your organization to help you manage or develop your marketplace. In fact, you can even have multiple marketplaces within the same organization.

## Environments
It is important to understand that Console is a way of viewing and manipulating the data on your marketplace. Each marketplace is specifically related to an environment. Each organization can have multiple environments. By default, there are three environments to consider: Test, Development (Dev), and Live. Each environment has its own data, and therefore the information that you can view and manipulate through Console will be exclusively related to the environment that you are working with. 

When you open your account, you have immediate access to the Test environment within the organization, and you can enable a Development environment when you want to start customizing your marketplace with code. Eventually, when you are ready to launch your marketplace, you can request a Live environment setup.

When you interact and create new users, listings, and transactions with your [test application](https://console.sharetribe.com), you will see the data in the Test environment. While you cannot modify the code of the Test environment, you can start playing around with it and see the changes you make reflected in the Test environment’s Console. You can create users, new listings, and even transactions and reviews. You can also configure your marketplace without code.

## Sections
Console is divided into three main sections: Home, Manage, and Build.

### Home
In the Home section, you find information about working with Sharetribe. You can find links to various resources that will help you understand, test, and build your custom marketplace, including:
- Your setup checklist for configuring your Test environment marketplace without code.
- Links to further resources to learn more about setting up your marketplace.
- Instructions on how to set up your local Sharetribe instance and links to the Sharetribe documentation and API reference, all of which you will need when you want to start developing your marketplace.
- Access to our Slack channel, where you can ask technical questions and engage with our technical team and the community.

### Manage
In the Manage section, you can find and engage with data about your users, listings, transactions, and reviews. This is the section you will use most in the day-to-day operations of your marketplace. You can delete users or listings, ban users, and edit default listing and user information. 

You can also export all this information into a CSV file to use as you see fit. Perhaps most importantly, you can add and modify the extended data of your marketplace elements in the Manage section. You can [read more about extended data](/docs/concepts/extended-data-introduction/).

### Build
In the Build section, you can modify some marketplace settings, content, and configurations without coding. When you have no-code content and configurations set up in Console, you can use the [Asset Delivery API](https://www.sharetribe.com/api-reference/asset-delivery-api.html) to fetch them to your custom marketplace application.
- [Read more about retrieving asset data](/docs/references/assets/#retrieving-asset-data)

General contains basic information about your marketplace: your marketplace ID, URL, and name. Here, you can also configure an outgoing email address, where all notifications to your marketplace’s users will be sent from.

## Side bar
In Console, you navigate between the different environments and sections through links in your sidebar. On the top of the sidebar, you can switch between the environments of your organization: Test, Development, and Live.

On the bottom of the sidebar, you can manage your account and organization by clicking your email address. You can change your password and generate personal API keys that you need for the Sharetribe command-line interface (CLI). You can also log out of Console here.

This is also where you add new admin users – the people who help you develop or manage your marketplace. Above your email address, you find links to our Help Center, as well as a link to contact our support.

## Action buttons
In addition to the sidebar, you still have a few action buttons on the top right of your screen:
- **View marketplace** takes you directly to the address you have defined as your Marketplace URL in Build > General.
- **Copy changes to...** is visible in your Test environment, and it will open a dialog that allows you to copy your configurations from the Test environment to either Live or Dev.
- **Go live** allows you to set up your Live environment.

---

## Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.

---
```