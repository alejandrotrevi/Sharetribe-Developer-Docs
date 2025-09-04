```markdown
---
title: "Requiring approval for new listings"
source: "provided"
extracted_date: "2024-08-09"
summary: "Sharetribe allows you to require operator approval for new listings before they are published. This article explains the feature and describes how to take listing approval into use in Sharetribe Console."
---

- [Table of Contents](#table-of-contents)
  - [Listing approval](#listing-approval)
  - [Managing listing approval in Sharetribe Console](#managing-listing-approval-in-sharetribe-console)
  - [Approving listings through Integration API](#approving-listings-through-integration-api)
  - [Elements indicating listing approval in Sharetribe Web Template](#elements-indicating-listing-approval-in-sharetribe-web-template)

# Requiring approval for new listings

Sharetribe allows you to require operator approval for new listings before they are published. This article explains the feature and describes how to take listing approval into use in Sharetribe Console.

## Listing approval

By default, listings in Sharetribe are published as soon as their author clicks the "publish" button. However, you can also set new listings to require approval before they are published. When the feature is enabled, new listings only get published once an operator has reviewed and approved them.

This feature can be used e.g. for checking that the listing is suitable for the marketplace. On marketplaces where providers have limitations to the number of listings they can post, this feature is useful for allowing certain providers to only post a certain number of listings.

Only new listings are queued for operator review. In other words, if a listing has already been approved and the provider edits the listing content, it will not require further approval. This is important to keep in mind if you use the feature to review content on the marketplace.

At the moment, there is no way to "decline" listing approval. If the listing requires editing to adhere to your marketplace standards, you can contact the provider with your suggestions on how to improve the listing.

Approving a listing does not trigger an automatic email notification. However, you can for instance [set up Zapier](https://www.sharetribe.com/help/en/articles/8529941#h_e678e79410) to listen to events where a listing's state goes from `pendingApproval` to `published` and send a message.

## Managing listing approval in Sharetribe Console

Listing approval can be enabled in your [Sharetribe Console](https://console.sharetribe.com/general), under Build > General. Listing approval is disabled by default.

![Listing approval toggle](https://docs/static/dba5763feb9ddd6431fc9d4d5d77f3d7/4daf6/listing-approval-toggle.png)

When your marketplace has listings requiring approval, you can see them in your Sharetribe Console Listings view. A listing requiring approval has a badge indicating its state.

![Pending approval badge](https://docs/static/acd38fb277c0c7527687eb05124f1b7c/8148d/pending-approval-badge.png)

When you click open the listing, the bottom of the listing panel shows a button to approve the listing.

![Pending approval button](https://docs/static/842f00bf779c0d0d3de22d292816ad23/60cf8/pending-approval-button.png)

## Approving listings through Integration API

In addition to Sharetribe Console, an operator can also create an integration to approve listings through the [Integration API](https://www.sharetribe.com/api-reference/integration.html#approve-listing) through the endpoint `integration_api/listings/approve`.

If, for instance, your marketplace allows non-premium users to post two listings and premium users to post five listings, you could create an Integration API script that automatically approves the listing if the user has unused listing quota.

## Elements indicating listing approval in Sharetribe Web Template

The Sharetribe Web Template shows UI elements related to pending listings by default.

When a user publishes a listing on a marketplace that requires operator approval, the listing state is set as `pendingApproval`. Listings in `pendingApproval` state are shown with a banner that lets the provider know that their listing is pending approval. The listing is not yet shown to other users.

![Pending approval banner](https://docs/static/4096caeb2d5c4cf100639fdccc9b27af/042c1/pending-approval-banner.png)

When the provider navigates to their Manage Listings page, listings pending approval are shown with an overlay indicating the listing's state.

![Listing card overlay](https://docs/static/99dbdbfc6233cc27476c14f4e41296d2/0bf1e/pending-approval-your-listings.png)

## Conclusion

If you have further questions or need assistance, reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```