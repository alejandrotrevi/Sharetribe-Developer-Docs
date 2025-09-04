```markdown
---
title: "Marketplace texts in Sharetribe"
source: "provided"
extracted_date: "2024-05-01"
summary: "This article introduces marketplace texts in Sharetribe and how they are modified and edited in Sharetribe Console."
---

- [Table of Contents](#table-of-contents)
  - [Marketplace texts in the Sharetribe Web Template](#marketplace-texts-in-the-sharetribe-web-template)
  - [Email texts in Sharetribe](#email-texts-in-sharetribe)
  - [How marketplace texts are handled in Sharetribe](#how-marketplace-texts-are-handled-in-sharetribe)
  - [Format for editing marketplace and email texts in Console](#format-for-editing-marketplace-and-email-texts-in-console)
  - [Simple argument](#simple-argument)
  - [Pluralization](#pluralization)
  - [Selection](#selection)
  - [Can I have a multilanguage marketplace?](#can-i-have-a-multilanguage-marketplace)

# Marketplace texts in Sharetribe
Marketplace texts cover all the small pieces of text in your marketplace's dynamic pages – button labels, error messages, and help texts, for example. Modifying marketplace texts to match your marketplace's theme and tone of voice is a key task in customizing any marketplace. [Read more about marketplace texts](https://www.sharetribe.com/help/en/collections/8975298-editing-text-content).

## Marketplace texts in the Sharetribe Web Template
In the Sharetribe Web Template, marketplace texts are not written directly into the source code. Instead, the source code uses [ICU message formatting](https://unicode-org.github.io/icu/userguide/format_parse/messages/) that defines keys for each meaningful piece of content. A translator or content creator can then define the message (i.e., the value) for each key in their language.

The key - value syntax is as follows:
```json
"<component name>.<marketplace text key>": "<marketplace text>"
```
For example:
```json
"ManageListingCard.editListing": "Edit listing"
```
The key is then used in the code, so that the code does not need to be changed even if the value ends up changing.
```javascript
// ManageListingCard.js uses the variable to identify the message
<FormattedMessage id="ManageListingCard.editListing" />
```

## Email texts in Sharetribe
Marketplace email templates use a similar formatting with a [custom Handlebar helper](https://docs.sharetribe.com/references/email-templates/#handlebars).
The key-value syntax is similar to what Sharetribe Web Template uses:
```json
"<email template name>.<email template section>": "<message>",
```
For example:
```json
"EmailChanged.Title": "Your email address was changed",
```

## How marketplace texts are handled in Sharetribe
Console-editable marketplace and email texts in Sharetribe are based on a concept of [assets](https://www.sharetribe.com/docs/references/assets/). Assets provide a way to define marketplace content and configurations using JSON files without needing to include the actual content in the client application codebase.
 
Marketplace texts are fetched to the client application through Asset Delivery API in JSON format. Assets can be retrieved by the latest version or by a specific version. Read more:
- [Marketplace assets](https://www.sharetribe.com/docs/references/assets/)
- [Handling hosted asset marketplace texts in the Sharetribe Web Template](https://www.sharetribe.com/docs/template/hosted-marketplace-texts/)

## Format for editing marketplace and email texts in Console
A piece of marketplace text using [ICU message formatting](https://unicode-org.github.io/icu/userguide/format_parse/messages/) can consist of a phrase:
```json
{
  "ManageListingCard.editListing": "Edit listing"
}
```
In the template, the phrase is then passed to the UI element that shows the value.
```javascript
<FormattedMessage id="ManageListingCard.editListing" />
```

## Simple argument
In addition, the format supports passing parameters as arguments to the marketplace text string. Passing a [simple argument](https://formatjs.io/docs/core-concepts/icu-syntax/#simple-argument) allows showing context-specific information as a part of the marketplace text string.
```json
{
  "ManageListingCard.pendingApproval": "{listingTitle} is pending admin approval. It's not visible on the marketplace yet."
}
```
```jsx
<Overlay
  message={intl.formatMessage({
    id: 'ManageListingCard.pendingApproval',
    values: { listingTitle }
  })}
/>
```

## Pluralization
One important factor in creating natural marketplace texts is handling pluralization in a text. The ICU format makes it possible to define different wordings for singular and plural options.
```json
{
  "ManageListingsPage.youHaveListings": "You have {count} {count, plural, one {listing} other {listings}}"
}
```

## Selection
In addition to pluralization options, you can build logic to the marketplace text strings using [select formatting](https://formatjs.io/docs/core-concepts/icu-syntax/#select-format).
```json
{
  "TransactionPage.ActivityFeed.default-purchase.purchased": "{actor, select, you {You placed an order for {listingTitle}.} other {{otherUsersName} placed an order for {listingTitle}.}}"
}
```

## Can I have a multilanguage marketplace?
Having several language-specific marketplace text files enables using a single application for multiple languages. However, editing marketplace texts in Console only supports one language at a time, so you will need to modify any other languages using bundled marketplace text files within your client application.

Read more about what to consider when [building a multilanguage Sharetribe marketplace on top of Sharetribe Web Template](https://docs.sharetribe.com/template/how-to-change-template-language/#developing-the-sharetribe-web-template-into-a-multilanguage-marketplace).
```