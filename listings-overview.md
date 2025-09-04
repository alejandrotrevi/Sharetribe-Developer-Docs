```markdown
---
title: "Listings in Sharetribe"
source: "provided"
extracted_date: "2024-10-29"
summary: "Sharetribe powers online marketplaces, and listings are at the core of any marketplace. This article explains the different aspects of listings in Sharetribe."
---

- [Overview](#overview)
- [Table of Contents](#table-of-contents)
  - [Listing lifecycle](#listing-lifecycle)
  - [Draft](#draft)
  - [Pending approval](#pending-approval)
  - [Published](#published)
  - [Closed](#closed)
  - [Deleted](#deleted)
  - [Listing extended data](#listing-extended-data)
  - [Custom listing search](#custom-listing-search)
  - [Different types of listings](#different-types-of-listings)

# Overview
Sharetribe powers online marketplaces, and listings are at the core of any marketplace. This article explains the different aspects of listings in Sharetribe.

## Listing lifecycle
Between being created and deleted, listings go through several different states. Depending on the listing's state, they are either returned with the public Listing endpoints or they are only available to the listing's author.

When tracking [listing events](https://www.sharetribe.com/docs/references/events/#supported-event-types) through the [Integration API](https://www.sharetribe.com/api-reference/integration.html), it is important to pay attention to the state of the listing.

### Draft
You may want to allow users to create listings as drafts first. Draft listings are not returned by the [listings/query](https://www.sharetribe.com/api-reference/marketplace.html#query-listings) endpoint but are visible to the author in Sharetribe Console.

Draft listings can be published using the [own_listings/publish_draft](https://www.sharetribe.com/api-reference/marketplace.html#publish-draft-listing) endpoint, and they can be discarded using the [own_listings/discard_draft](https://www.sharetribe.com/api-reference/marketplace.html#discard-draft-listing) endpoint.

### Pending approval
If the marketplace [requires listing approval for new listings](https://www.sharetribe.com/docs/concepts/requiring-approval/), a draft listing moves into **state: pendingApproval** when the [own_listings/publish_draft](https://www.sharetribe.com/api-reference/marketplace.html#publish-draft-listing) is called.

### Published
Published listings are returned by [listings/query](https://www.sharetribe.com/api-reference/marketplace.html#query-listings) and [listings/show](https://www.sharetribe.com/api-reference/marketplace.html#show-listing) endpoints. 

### Closed
A listing can be closed by both the author and the marketplace operator. A closed listing is not returned by [listings/query](https://www.sharetribe.com/api-reference/marketplace.html#query-listings) but is still visible to the author.

### Deleted
Listings can only be deleted by the operator in Sharetribe Console. Deleted listings do not get returned by any listing endpoints.

## Listing extended data
Listings are one of the resource types in Sharetribe where you can add [extended data](https://www.sharetribe.com/docs/references/extended-data/). Extended data allows for custom attributes beyond the default ones offered by Sharetribe.

### Custom listing search
You can query listings by public extended data attributes in addition to default parameters. 

### Different types of listings
You can create service listings and product listings within the same marketplace and differentiate them with a **listingType** attribute in listing extended data.

For instance, for a service marketplace, you could model service provider listings as bookable profiles.
```