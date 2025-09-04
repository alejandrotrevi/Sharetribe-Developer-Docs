```
---
title: "Listing Extended Data"
source: "provided"
extracted_date: "2024-04-24"
summary: "Listing extended data allows you to use the powerful search engine in Sharetribe, as well as customize your listing information."
---

- [Viewing and modifying listing extended data](#viewing-and-modifying-listing-extended-data)
- [Search and filtering](#search-and-filtering)
- [Different types of listings](#different-types-of-listings)

# Listing Extended Data
Listing extended data allows you to use the powerful search engine in Sharetribe, as well as customize your listing information.

## Overview
Listings can have three types of extended data: public, private, and metadata. This article gives an overview of using these different extended data types.

### Information
Listing public data fields can be configured in Console using [assets](https://www.sharetribe.com/docs/references/assets/). The Sharetribe Web Template has the capability to read the asset-based public data fields and display the necessary components when editing and viewing a listing.

## Table of Contents
- [Viewing and modifying listing extended data](#viewing-and-modifying-listing-extended-data)
- [Search and filtering](#search-and-filtering)
- [Different types of listings](#different-types-of-listings)

## Viewing and Modifying Listing Extended Data
Public data and metadata are visible to everyone – in other words, they are available when querying listings through the [public listing endpoints](https://www.sharetribe.com/api-reference/marketplace.html#listings) in Marketplace API. Public data and metadata can be used, for instance, to distinguish different types of listings from each other, or to allow marketplace users to filter and search for specific features on a listing. Operators can use metadata to categorize listings to regular and premium, for instance.

On the other hand, listing private data is available through the [ownListing endpoints](https://www.sharetribe.com/api-reference/marketplace.html#own-listings) in Marketplace API and [listing endpoints](https://www.sharetribe.com/api-reference/integration.html#listings) in Integration API.

Private data can be used to allow the listing author to make private notes on the listing, since the information is not visible for the general marketplace audience.

The listing’s author can modify the listing’s public and private data through the [ownListing](https://www.sharetribe.com/api-reference/marketplace.html#own-listings) create and update endpoints. An operator can modify all listing extended data, either through [Integration API](https://www.sharetribe.com/api-reference/integration.html#listings) or in Sharetribe Console.

## Search and Filtering
How users search and filter listings is a vital part of their experience in your marketplace. A smooth search experience allows them to find the listings they’re interested in effortlessly, and the right filters help them narrow down results to a selection that best fits their needs. Extended data helps you build the custom search and filtering experience your marketplace needs.

Listings can be searched by keyword or location using Sharetribe’s [powerful built-in search feature](/docs/concepts/how-the-listing-search-works/). In addition to this, you can use listing public extended data and metadata to create a variety of different types of filters; for example, a filter can be a slider with a range of values or a checkbox group. You can also specify how listings are prioritized and sorted in the results. Extended data is not available for search or sorting by default, which means you are in control of building your own, unique search experience.

When planning your search experience, think about the following questions: 
- Do you want the extended data in any given field to be searchable? 
- Do you want it to be a filter as well as a search parameter? 
- What kind of filter should it be? 
- Which extended data should be prioritized in search results?

Maybe your marketplace charges a membership fee, and you want listings from sellers in your highest subscription tier to be displayed first. Or perhaps your marketplace is for selling preowned clothing, and you want your users to be able to filter by size to find the best fit. The possibilities are numerous!

## Different Types of Listings
A marketplace can have several types of listings — rentals and sales, events and facilities. Listing extended data is a powerful way to distinguish different listing types. Read more about the possibilities for [different listing types in Sharetribe](https://www.sharetribe.com/docs/concepts/listings-overview/#different-types-of-listings).
```