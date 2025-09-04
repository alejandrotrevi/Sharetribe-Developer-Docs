```markdown
---
title: "Assets"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2025-01-29"
summary: "Reference documentation providing information on assets."
---

- [Overview](#overview)
- [Asset Versioning](#asset-versioning)
- [Retrieving Asset Data](#retrieving-asset-data)
- [Asset Data Caching](#asset-data-caching)
- [Available Default Asset Files](#available-default-asset-files)
- [Further Reading](#further-reading)

# Overview
Assets are a mechanism for defining client application configuration data and content for a marketplace. The assets are managed by a marketplace operator and change relatively infrequently. Each asset is an object that defines a path and content. The path gives the asset a name and allows organizing assets in a way similar to a file system, where assets can be organized in a directory-like structure. The content of the asset is its data in one of the supported data types.

At present, Sharetribe supports only JSON data as assets. Support for images (JPEG, PNG, etc.) is coming in the future.

The assets are typically edited by a marketplace operator through Sharetribe Console.

## Example Assets Structure
```shell
├── content
│   └── translations.json
└── design
    └── branding.json
```
In this example, `design/branding.json` could contain configuration data (for instance, UI colors, logo, etc.) and `content/translations.json` could contain string marketplace text data to be used in a marketplace client application (such as one based on one of the Sharetribe Web Template).

## Asset Versioning
Collectively, the set of assets of a marketplace is called the **asset tree**. Each time the data of one or more assets is changed, a new asset is added, or an existing asset is deleted, a new version of the asset tree is created. Old asset tree versions are never updated. In other words, tree versions are immutable.

Each tree version, therefore, represents the exact set of assets and their data at the time when the version was created. The following analogy with the Git version control system may be useful to consider: The asset tree versions are analogous to Git commit SHAs. The entire asset tree is versioned as a whole, and individual assets do not have their own independent versions. Unlike Git, however, the asset tree versioning does not support branching.

To facilitate access to the latest asset data, Sharetribe maintains a built-in **alias** called `latest` that always refers to the latest asset tree version.

## Retrieving Asset Data
Client applications retrieve asset data through the [Asset Delivery API](https://www.sharetribe.com/api-reference/asset-delivery-api.html). Asset data can be accessed either by alias (using the built-in `latest` alias) or by specific version.

In order to access asset data, clients need a client ID for a valid Sharetribe Marketplace API application. The easiest way to access assets is by using the [Sharetribe SDK for JavaScript](#). See the [Asset Delivery API reference](https://www.sharetribe.com/api-reference/asset-delivery-api.html) for more information.

### Asset Data Caching
In order to ensure efficient data retrieval, the Asset Delivery API response data can be cached by both the Asset CDN and the client. Each API response comes with appropriate `Cache-Control` and `ETag` HTTP headers automatically and caching works out of the box with clients that support these headers (such as end users' web browsers).

Since asset versions are immutable, asset data that is accessed by a specific version can be cached for an extended period of time. On the other hand, the `latest` alias is mutable, and therefore asset data retrieved by alias cannot be cached indefinitely. The cache time for access by alias can differ depending on whether your marketplace environment is a development or live one. For live marketplaces, the cache time can be up to 5 minutes, while for test and development marketplaces, it is much lower. Refer to the [Asset Delivery API reference](https://www.sharetribe.com/api-reference/asset-delivery-api.html) for up-to-date information.

## Available Default Asset Files
Assets are fetched by file path. The asset files available to be fetched by default are the following:

### General
- Localization: `/general/localization.json`
- Access control: `/general/access-control.json`

### Content
- Content pages: `/content/pages/{pageId}.json`
- Top bar: `/content/top-bar.json`
- Footer: `/content/footer.json`
- Marketplace texts: `/content/translations.json`
- Email texts: `/content/email-texts.json`

### Design
- Branding: `/design/branding.json`
- Layout: `/design/layout.json`

### Users
- User types: `/users/user-types.json`
- User fields: `/users/user-fields.json`

### Listings
- Listing types: `/listings/listing-types.json`
- Listing categories: `/listings/listing-categories.json`
- Listing fields: `/listings/listing-fields.json`
- Listing search: `/listings/listing-search.json`

### Transactions
- Minimum transaction size: `/transactions/minimum-transaction-size.json`

### Monetization
- Commission: `/transactions/commission.json`

### Integrations
- Map: `/integrations/map.json`
- Analytics: `/integrations/analytics.json`
- Google Search Console: `/integrations/google-search-console.json`

## Further Reading
- [Editing client application marketplace texts](https://www.sharetribe.com/docs/concepts/marketplace-texts/)
- [Managing asset-based marketplace content](https://www.sharetribe.com/docs/concepts/content-management/)
- [Asset Delivery API reference](https://www.sharetribe.com/api-reference/asset-delivery-api.html)

```