```markdown
---
title: "Configuration variables"
source: "provided"
extracted_date: "2023-10-24"
summary: "The Sharetribe Web Template has many configurations you can edit through configuration files. This article explains how to change those configurations and what they mean."
---

- [Table of Contents](#table-of-contents)
- [Using Console for configurations](#using-console-for-configurations)
- [Configuration files](#configuration-files)
- [Branding](#branding)
- [Common configurations](#common-configurations)
- [Currency and pricing](#currency-and-pricing)
- [Locale](#locale)
- [Structured data](#structured-data)
- [Layout configuration](#layout-configuration)
- [Search page](#search-page)
- [Listing images](#listing-images)
- [Listing image aspect ratio](#listing-image-aspect-ratio)
- [Listing configuration](#listing-configuration)
- [Extended data configuration](#extended-data-configuration)
- [Listing type configurations](#listing-type-configurations)
- [Search configuration](#search-configuration)
- [Search filters](#search-filters)
- [Sorting](#sorting)
- [Map configurations](#map-configurations)
- [Stripe and transactions](#stripe-and-transactions)
- [Stripe configurations](#stripe-configurations)

# Configuration variables
The Sharetribe Web Template has many configurations you can edit through configuration files. This article explains how to change those configurations and what they mean.

## Using Console for configurations
Console allows users to configure many configuration variables through a new no-code interface. The newest release of The Sharetribe Web Template supports fetching the configuration data through the [Asset Delivery API](https://www.sharetribe.com/api-reference/asset-delivery-api.html). If you are using an older version of the web-template, follow the instructions [here](/docs/template/how-to-customize-template/#pull-in-the-latest-upstream-changes) to pull in the latest upstream changes.

**Information**: Settings configured in local configuration files are overridden by any fetched via the Asset Delivery API. You can refer to [this article](/docs/template/hosted-and-local-configurations/) to modify the way your template merges local and hosted configurations.

## Configuration files
All relevant configuration is split between the following files and the environment variables. You can see the available environment variables in the [template environment variables](https://github.com/sharetribe/web-template/blob/main/src/config/template-env.js) article.

- [configBranding.js](https://github.com/sharetribe/web-template/blob/main/src/config/configBranding.js)
  - Marketplace colour, logos, Facebook and Twitter media
- [configDefault.js](https://github.com/sharetribe/web-template/blob/main/src/config/configDefault.js)
  - Change localization settings, marketplace currency and add social media links
- [configLayout.js](https://github.com/sharetribe/web-template/blob/main/src/config/configLayout.js)
  - Layout of the search and listing page and aspect ratio for listing image variants
- [configListing.js](https://github.com/sharetribe/web-template/blob/main/src/config/configListing.js)
  - Define extended data variables and how they are displayed on both the listing and search page.
- [configSearch.js](https://github.com/sharetribe/web-template/blob/main/src/config/configSearch.js)
  - Change between keyword or location search and set default search filters and sorting options
- [configMaps.js](https://github.com/sharetribe/web-template/blob/main/src/config/configMaps.js)
  - Load maps through Google or Mapbox and location-related search settings
- [configStripe.js](https://github.com/sharetribe/web-template/blob/main/src/config/configStripe.js)
  - The configuration that Stripe needs to function correctly
- [configDefaultLocationSearches.js](https://github.com/sharetribe/web-template/blob/main/src/config/configDefaultLocationSearches.js)
  - Specify locations shown to the user as suggestions when they click on the search bar

## Branding
The [configBranding.js](https://github.com/sharetribe/web-template/blob/main/src/config/configBranding.js) file allows you to customize the branding and appearance of your marketplace. Configurations include:
- The `marketplaceColor` constant defines the primary color for your marketplace.
- The `logoImageDesktopURL` and `logoImageMobileURL` constants specify the URL for the desktop and mobile logos.
- The `brandImageURL` constant specifies a URL for a background image used on several pages.
- The `facebookImageURL` and `twitterImageURL` constants specify default images for social media sharing.

## Common configurations
You can find all common configurations in the [configDefault.js](https://github.com/sharetribe/web-template/blob/main/src/config/configDefault.js) file. Notable settings include:
- The `marketplaceName` setting specifies the name of your marketplace.

### Currency and pricing
The currency setting specifies the currency used in your marketplace. It must be an ISO 4217 currency code. The `listingMinimumPriceSubUnits` setting specifies the minimum price for a listing, expressed in currency subunits (e.g., cents).

### Locale
Use the localization setting to specify the locale and first day of the week. The setting contains two properties:
- `locale`: The locale code (default is 'en').
- `firstDayOfWeek`: A number (0 for Sunday, 6 for Saturday).

### Structured data
The `siteFacebookPage`, `siteInstagramPage`, and `siteTwitterHandle` settings specify the Schema.org organizational metadata. You can specify address information such as:
- `addressCountry`: The country in ISO 3166-1 alpha-2 format.
- `addressRegion`: The region or state.
- `postalCode`: The postal code.
- `streetAddress`: The street address.

## Layout configuration
Find layout options in the [configLayout.js](https://github.com/sharetribe/web-template/blob/main/src/config/configLayout.js) file. Key configurations include:
- Options to toggle how the search page is rendered and how listing images are displayed.

### Search page
The SearchPage component of the template has two layout variants: 'map' and 'grid'.

```js
// There are 2 SearchPage variants that can be used:
// 'map' & 'grid'
export const searchPage = {
  variantType: 'map',
};
```

### Listing images
You can toggle between two options for listing images on the listing page:
- The `'coverPhoto'` layout option shows a hero section.
- The `'carousel'` layout option displays an image carousel.

```js
export const listingPage = {
  variantType: 'carousel',
};
```

### Listing image aspect ratio
Use the `listingImage` option to specify the aspect ratio for listing images. 

```js
export const listingImage = {
  variantType: 'cropImage',
  aspectRatio: '3/1',
};
```

## Listing configuration
### Extended data configuration
The `listingFields` array allows you to add extended data fields to listings. Each object represents an extended data field.

```js
{
  key: 'frame',
  scope: 'public',
  schemaType: 'enum',
  enumOptions: [
    { option: 'aluminium', label: 'Aluminium' },
    { option: 'steel', label: 'Steel' },
    { option: 'titanium', label: 'Titanium' },
  ],
};
```

## Listing type configurations
The [configListing.js](https://github.com/sharetribe/web-template/blob/main/src/config/configListing.js) file contains an array of listing types and their transaction process configurations. 

## Search configuration
The [configSearch.js](https://github.com/sharetribe/web-template/blob/main/src/config/configSearch.js) file allows you to adjust search filters and listing sorting options.

### Search filters
In the `configSearch.js` file, you can change the search type by toggling the `mainSearch` variable.

### Sorting
You can adjust sorting options through the `sortConfig`.

## Map configurations
The [configMaps.js](https://github.com/sharetribe/web-template/blob/main/src/config/configMaps.js) file allows you to set up a map provider.

## Stripe and transactions
### Stripe configurations
The [configStripe.js](https://github.com/sharetribe/web-template/blob/main/src/config/configStripe.js) file includes all countries supported by the Sharetribe Stripe integration.

---

**Can’t find what you’re looking for?**
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```