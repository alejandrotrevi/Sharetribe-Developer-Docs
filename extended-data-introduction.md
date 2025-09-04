```markdown
---
title: "Introduction to Extended Data"
source: "provided"
extracted_date: "2024-11-14"
summary: "Extended data allows you to fully customize the information you want to collect about users, listings, and transactions on your marketplace."
---

- [Why Extended Data?](#why-extended-data)
- [Types of Extended Data](#types-of-extended-data)
  - [Access to Edit](#access-to-edit)
  - [Access to View](#access-to-view)
- [Using Extended Data](#using-extended-data)
  - [1. Public Data](#1-public-data)
  - [2. Protected Data](#2-protected-data)
  - [3. Private Data](#3-private-data)
  - [4. Public Metadata](#4-public-metadata)
  - [5. Protected Metadata](#5-protected-metadata)
- [Getting Started with Extended Data](#getting-started-with-extended-data)

# Introduction to Extended Data
Extended data allows you to fully customize the information you want to collect about users, listings, and transactions on your marketplace.

## Why Extended Data?
Extended data is a Sharetribe feature that allows you to customize your user, listing, and transaction data. Your marketplace has its own unique offering and requires specific data that other marketplaces do not. Maybe you’re building a marketplace for cooking classes and want to ask chefs how many years of experience they have. Or perhaps you’re building a summer cottage rental community and want your providers to define the amenities of their cottage. Extended data gives you the freedom to determine exactly what information you want your users to provide on your marketplace and how. However, the possibilities of extended data do not end there!

Extended data can be customized to different use-cases to fit your exact needs: in addition to collecting the information you need from your users in the form you choose, it allows you to display featured listings, have different user types, build custom search functionality, and much more.

With extended data, you can build integrations with third-party services, such as a subscription payment system or SMS notification software. You can also have extended data that is only revealed at a specific point in a transaction. Or maybe you want more control over how search results on your marketplace are prioritized and sorted? For all these customizations, extended data is your friend.

The possibilities you have with extended data are vast. In the next section, we’ll discuss each type of extended data in more detail and offer examples of what they can be used for.

## Types of Extended Data
There are six possible types of extended data, defined by who can edit and view them. Five out of these are available in Sharetribe at this time. They are *public data*, *protected data*, and *private data*, as well as *public metadata* and *protected metadata*.

In the following sections, “author” means the user who created the listing or profile in question. “Operator” refers to both the marketplace owner and the Integration API. The marketplace operators and the Integration API have access to view and edit all of the data types.

### Access to Edit
Extended *data* can be written and edited by listing or user profile authors in your frontend application. *Metadata* can be written and edited only by marketplace operators.

### Access to View
Public data and public metadata can be viewed by everyone with access to your marketplace. Protected data is private by default, but can be viewed at a certain point during a transaction process by members of that transaction. Protected metadata is visible to the participants of the transaction. Private data can only be viewed by the listing or user profile authors themselves.

We can also organize the data types by placing them in a table.

|           | Data                                        | Metadata                             |
|-----------|---------------------------------------------|--------------------------------------|
| Public    | editing: author, operator – viewing: all users | editing: operator – viewing: all users |
| Protected | editing: author, operator – viewing: transaction members | editing: operator – viewing: transaction members |
| Private   | editing: author, operator – viewing: author, operator | not available                        |

In order to determine what type of extended data you want to collect on your marketplace, you need to answer the following questions:
- What information do you want to collect about your users and listings and during transactions?
- Who can write and edit that information?
- What information do you want to display and to whom?

In the next section, we’ll explore how different types of extended data are shown on your marketplace and Console and offer examples of the possibilities the different types of extended data provide.

## Using Extended Data
### 1. Public Data
Public data is information that is visible to all users of your marketplace and can be written and edited by listing authors or user profile owners. It can help your customers make purchasing decisions, let your customers know important details about your sellers, or be used as search filters and parameters and to sort search results. Public data allows you to customize your public listing and user information to fit your needs exactly!

Let’s look at listing public data in action. Here is a listing from an imaginary bike rental marketplace, Biketribe.

#### Examples of Public Extended Data

![Listing public data in the frontend](https://example.com/docs/static/extended-data-frontend-view-7aa2ed03ca2adfb0cfa102567feeba60.png)

We’ll look at a few different extended data fields. We have highlighted the fields in different colors: blue is for tire size, and green is for bike brand.

### 2. Protected Data
Protected data is information that can be revealed at specific points of the transaction process. It can only be seen by the parties taking part in the transaction, meaning the provider, customer, and the marketplace operator. After a cooking class booking is confirmed, you might want to request the customer to provide information on any dietary restrictions. Or maybe you only want to reveal a provider's phone number or address after payment has been confirmed to guarantee your users do not bypass your payment system. These cases can be handled with protected data.

### 3. Private Data
Private data can only be edited and viewed by those who created the listing or user profile in question and marketplace operators. It is similar to protected data but is not intended to be revealed during the transaction process. Private data can be used to collect and store information about users or listings that is important for marketplace operators but does not need to or should not be revealed to other users.

### 4. Public Metadata
Public metadata is visible to all users, but only the operator and the Integration API can edit it. Typical use-cases for metadata are featured listings or premium users. You may want to curate listings that get this extra visibility yourself or offer it as a paid service, so using public data, which the users can edit themselves, is not an option. This is where metadata comes into play.

### 5. Protected Metadata
Transactions can also have metadata. It can only be seen by the transaction members as it is tied to the transaction. An example of transaction metadata could be a unique Zoom link to where an online service will take place.

## Getting Started with Extended Data
Extended data is a powerful feature that allows you to customize your marketplace’s offering, whether services, rentals, or products, to your exact needs. It helps you collect the information you require from your users and enables additional functionality together with the transaction process and through the Integration API. Extended data also plays a vital role in search result sorting and filtering.

To get started with extended data, you should decide what information you want to collect about your users and listings or what is important for users to know during transactions. Next, you should think about who has access to edit that information. Finally, you should consider whether you want to display this information to everyone, select users, or just to yourself. Through extended data, Sharetribe can support a multitude of different kinds of listings, monetization models, user profiles, and so on. You can create the exact data structure you need for your marketplace.

---

## Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```