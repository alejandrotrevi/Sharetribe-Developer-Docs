```markdown
---
title: "Change Search Filters in Sharetribe Web Template"
source: "provided"
extracted_date: "2023-10-24"
summary: "This guide describes how to change the search filters in Sharetribe Web Template."
---

- [Filter types](#filter-types)
- [Keyword filter](#keyword-filter)
- [Adding a new search filter](#adding-a-new-search-filter)
- [Common changes](#common-changes)
- [Creating your own filter types](#creating-your-own-filter-types)

# Change Search Filters in Sharetribe Web Template

This guide describes how to change the search filters in Sharetribe Web Template.

The search experience can be improved by adding search filters to narrow down the results. The filters rely on listing's indexed data.

There are 3 different UI contexts that render filters. On the mobile layout, all filters are rendered to a modal inside the `SearchFiltersMobile` component. On the desktop layout, the most important filters are in `SearchFiltersPrimary`, and extra filters are in the `SearchFiltersSecondary` panel, which opens when the user clicks the *"More filters"* button.

## Filter types

Sharetribe Web Template has several different filter types: *BookingDateRangeFilter*, *KeywordFilter*, *PriceFilter*, *SelectSingleFilter*, and *SelectMultipleFilter*. Select single and select multiple filters are generic – they can be used to filter search results using different kinds of data. The price and date range filters, on the other hand, are only used for filtering by price and date range. Listings with hourly bookings can also be filtered by their availability in a date range with an optional minimum duration. Keyword filter is a special case - more about that later.

*SelectSingleFilter* and *SelectMultipleFilter* can be used with extended data. The *SelectSingleFilter* can be used to filter out listings with only one string value in a related public data field. For instance, a listing's publicData attribute could contain an attribute `condition: 'new'`. The related Marketplace API listing query could then be made with the query parameter `pub_condition=new`.

The *SelectMultipleFilter*, on the other hand, can take multiple values for a single search parameter. In this case, a listing entity could contain public data `accessories: ['bell', 'lights', 'lock']` and the query parameter to retrieve that listing among other search results could be `pub_accessories=has_any:bell,mudguard`.

We will tell you more about these filters later on in this article.

## Keyword filter

The keyword filter works a bit differently from the other filters. It does filter search results, but it also sorts those results according to how strongly the listing's data (title, description, and possible extended data) correlates with the search string. Currently, there is no decay function that would map keyword match correlation with distance to `origin`, so the *origin* param cannot be used at the same time as the *keyword* param. You can read more about how the keyword search works in the related [concepts article](/docs/concepts/how-the-listing-search-works/).

![Desktop filters](https://www.sharetribe.com/docs/static/9f4fda999ad5ac0256952fadb8ed4191/53fb6/keyword-search.png)

It is possible to remove location search from topbar and replace it with the keyword search or use them together (without origin param). You can configure the main search type in **src/config/configSearch.js**.

## Adding a new search filter

Next we'll guide you through the steps of adding a *gears* filter to the marketplace.

First step for adding a new filter is to make sure that the data being used for filtering is saved in the listing's `publicData` attribute. On how to achieve this, please refer to the [Extend listing data in Sharetribe Web Template](https://www.sharetribe.com/docs/how-to/extend-listing-data-in-template/) how-to guide. Another aspect in search filters is that a [search schema](https://www.sharetribe.com/docs/references/extended-data/#search-schema) needs to be added to the data in order for API to index it for search. Adding search schema can be done by the [Sharetribe CLI](https://www.sharetribe.com/docs/introduction/getting-started-with-sharetribe-cli/).

Once a public data attribute is added to the listings and the data attribute is indexed, the listing searches can be filtered by that attribute by adding a query parameter that consists of a preceding "pub_" and the attribute name, so for the *capacity* attribute the parameter would be "pub_capacity". You might have guessed that "pub_" refers to **public data**. Similarly, "meta_" would refer to **metadata**, which is another type of extended data.

Further reading on public data can be found in the [listing extended data concepts article](https://www.sharetribe.com/docs/concepts/listing-extended-data/) and the [Extend listing data in Sharetribe Web Template](https://www.sharetribe.com/docs/how-to/extend-listing-data-in-template/) how-to guide.

In Sharetribe, only top-level attributes can be indexed i.e. used for search. If you have a public data attribute with nested attributes, it is not possible to create a search schema for those. So instead of using a nested attribute:

```jsx
publicData: {
  instrumentProficiency: {
    // These attributes cannot be indexed for search in Sharetribe
    violin: 'professional',
    guitar: 'intermediate',
    tuba: 'beginner',
  },
}
```

you would need to set all attributes you want to query as top-level attributes:

```js
publicData: {
  // These attributes can be indexed for search in Sharetribe
  violinProficiency: 'professional',
  guitarProficiency: 'intermediate',
  tubaProficiency: 'beginner',
}
```

### Common changes

A few common changes are required to add a select single or a select multiple filter to desktop and mobile views.

In the default Sharetribe Web Template, listing extended data configurations are defined in the `configListing.js` file:

```
└── src
    └── config
        └── configListing.js
```

There you need to add the *gears* attribute's configurations to the `listingFields` array:

```js
{
  key: 'gears',
  scope: 'public',
  schemaType: 'enum',
  enumOptions: [
    { option: '1', label: 'Single speed' },
    { option: '2to3', label: '2 to 3' },
    { option: '4to7', label: '4 to 7' },
    { option: '8to15', label: '8 to 15' },
    { option: '16to24', label: '16 to 24' },
    { option: '25+', label: 'Over 25' },
  ],
  filterConfig: {
    indexForSearch: true,
    label: 'Gears',
    group: 'secondary',
  },
  showConfig: {
    label: 'Gears',
    isDetail: true,
  },
  saveConfig: {
    label: 'Gears',
    placeholderMessage: 'Select number of gears...',
    isRequired: true,
    requiredMessage: 'You need to select number of gears',
  },
}
```

### Creating your own filter types

If you are creating new filter types, note that we are using two different types of components: popup and plain. Popup components are rendered as primary dropdowns in the search view in `SearchFiltersPrimary` component. Plain components are used with `SearchFiltersMobile` and `SearchFiltersSecondary`. *SearchFiltersSecondary* opens secondary filters in a distinct panel in order to fit additional filters to the desktop search view.

To make creating new filters easier, there are two generic components: `FilterPopup` and `FilterPlain`. These components expect that you give form fields as child components. Check `SelectMultipleFilter` to see how these components work.

When you have your custom filter component ready, you need to add it to **SearchPage/FilterComponent.js**:

```
└── src
    └── containers
        └── SearchPage
            └── FilterComponent.js
```

Settings configured in local configuration files are overridden by any fetched via the Asset Delivery API. You can refer to [this article](https://www.sharetribe.com/docs/template/hosted-and-local-configurations/) to modify the way your template merges local and hosted configurations.

---

## Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.

© [Sharetribe](https://www.sharetribe.com) 2025.
```