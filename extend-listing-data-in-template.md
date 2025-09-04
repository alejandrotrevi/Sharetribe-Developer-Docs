```markdown
---
title: "Extend listing data in Sharetribe Web Template"
source: "provided"
extracted_date: "2025-05-06"
summary: "This guide describes how to use extended data to expand the listing data model in Sharetribe Web Template."
---

- [Overview](#overview)
- [Table of Contents](#table-of-contents)
- [Add a new top-level attribute](#add-a-new-top-level-attribute)
- [Declaring the attribute and its possible values](#declaring-the-attribute-and-its-possible-values)
- [Configuring the listing detail editing page](#configuring-the-listing-detail-editing-page)
- [Configuring search](#configuring-search)
- [Configuring the listing page](#configuring-the-listing-page)

# Overview
This guide shows you a code-based approach to expanding the listing data model in your marketplace. We'll have a look at how the listing can be configured so that data gets added and how it can then be presented and used to filter searches. 

Adding new attributes to the data model relies on [extended data](https://www.sharetribe.com/docs/references/extended-data/). In Sharetribe Web Template, top-level listing extended data is configured in the [configListing.js](https://github.com/sharetribe/web-template/blob/main/src/config/configListing.js) file.

```shell
└── src
    └── config
        └── configListing.js
```

# Table of Contents
- [Add a new top-level attribute](#add-a-new-top-level-attribute)
- [Declaring the attribute and its possible values](#declaring-the-attribute-and-its-possible-values)
- [Configuring the listing detail editing page](#configuring-the-listing-detail-editing-page)
- [Configuring search](#configuring-search)
- [Configuring the listing page](#configuring-the-listing-page)

## Add a new top-level attribute
Let's extend the default bike-related listing data by adding an attribute `accessories` to show what accessories are included. The full configuration looks like this:

```js
{
    key: 'accessories',
    scope: 'public',
    schemaType: 'multi-enum',
    enumOptions: [
      { option: 'bell', label: 'Bell' },
      { option: 'lights', label: 'Lights' },
      { option: 'lock', label: 'Lock' },
      { option: 'mudguard', label: 'Mudguard' },
    ],
    saveConfig: {
      label: 'Accessories',
      placeholderMessage: 'Select an option…',
      isRequired: false,
    },
    filterConfig: {
      indexForSearch: true,
      label: 'Accessories',
      searchMode: 'has_any',
      group: 'secondary',
    },
    showConfig: {
      label: 'Accessories',
    },
}
```

## Declaring the attribute and its possible values
Extended data attributes in the *configListing.js* file need to be defined, at minimum, by **key**, **scope**, and **schemaType**.

```js
key: 'accessories',
scope: 'public',
schemaType: 'multi-enum',
enumOptions: [
  { option: 'bell', label: 'Bell' },
  { option: 'lights', label: 'Lights' },
  { option: 'lock', label: 'Lock' },
  { option: 'mudguard', label: 'Mudguard' },
],
```

- **enum** attributes are saved as a single string value from a list of predefined options.
- **multi-enum** attributes are saved as an array of string values from a list of predefined options.
- **boolean** attributes are saved as `true` or `false` boolean values.
- **long** attributes are saved as an 8-byte whole number.
- **text** attributes are saved as a single text entry.

## Configuring the listing detail editing page
The *EditListingDetailsPanel* is configured to show specific inputs for specific schema types.

```js
saveConfig: {
  label: 'Accessories',
  placeholderMessage: 'Select an option…',
  isRequired: false,
},
```

## Configuring search
Top-level attributes can be set as searchable, but you might have listing attributes you do not want to use for filtering listings.

```js
filterConfig: {
  indexForSearch: true,
  label: 'Accessories',
  searchMode: 'has_any',
  group: 'secondary',
},
```

For multi-enum attributes, you can define whether you want to show listings with all the query attributes (*has_all*) or listings with any of the query attributes (*has_any*).

## Configuring the listing page
The configuration for showing top-level extended data on the listing page is straightforward.

```js
showConfig: {
  label: 'Accessories',
},
```

And that is it! With this configuration, the attribute can be added to the listing, used for search, and shown on the listing page.

# Additional Information
If there are existing listings, they don't get an update before their extended data is updated by a provider. Operators can also do this one-by-one through Console or through the Integration API. So, you should assume that there are listings without these new extended data fields and, therefore, there should be safeguards against undefined values.

---

**Can't find what you're looking for?**  
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the [Sharetribe Developer Community at the Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```