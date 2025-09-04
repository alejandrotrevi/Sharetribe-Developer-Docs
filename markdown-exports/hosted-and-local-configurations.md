```markdown
---
title: "How to manage hosted and local configurations"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2023-10-24"
summary: "The Sharetribe Web Template has both local and asset-based configurations available. Learn how to combine both types of configurations."
---

- [Table of Contents](#table-of-contents)
  - [How configurations are merged in the template](#how-configurations-are-merged-in-the-template)
  - [Example: use both hosted and local configs for listing types](#example-use-both-hosted-and-local-configs-for-listing-types)

# How to manage hosted and local configurations

The Sharetribe Web Template has both local and asset-based configurations available. Learn how to combine both types of configurations.

## How configurations are merged in the template

The template uses a function [`mergeConfig`](https://github.com/sharetribe/web-template/blob/main/src/util/configHelpers.js#L667) in *src/util/configHelpers.js* to handle the configurations.

```
└── src
    └── util
        └── configHelpers.js
```

```jsx
export const mergeConfig = (configAsset = {}, defaultConfigs = {}) => {
  const listingMinimumPriceSubUnits =
    getListingMinimumPrice(configAsset.transactionSize) ||
    defaultConfigs.listingMinimumPriceSubUnits;

  return {
    ...defaultConfigs,
    listingMinimumPriceSubUnits,
    analytics: mergeAnalyticsConfig(
      configAsset.analytics,
      defaultConfigs.analytics
    ),
    branding: mergeBranding(
      configAsset.branding,
      defaultConfigs.branding
    ),
    layout: mergeLayouts(configAsset.layout, defaultConfigs.layout),
    listing: mergeListingConfig(configAsset, defaultConfigs),
    search: mergeSearchConfig(
      configAsset.search,
      defaultConfigs.search
    ),
    maps: mergeMapConfig(configAsset.maps, defaultConfigs.maps),
    footer: configAsset.footer,
    hasMandatoryConfigurations: hasMandatoryConfigs(configAsset),
  };
};
```

Within the *mergeConfig* function, each different configuration category has a separate merge function.

For example, let's take a look at how to modify the *mergeListingConfig* function to include listing types from both hosted asset-based configurations and local configurations.

```jsx
// Note: by default, listing types and fields are only merged if explicitly set for debugging
const mergeListingConfig = (hostedConfig, defaultConfigs) => {
  const hostedListingTypes = restructureListingTypes(
    hostedConfig.listingTypes?.listingTypes
  );
  const hostedListingFields = restructureListingFields(
    hostedConfig.listingFields?.listingFields
  );

  const { listingTypes: defaultListingTypes, listingFields: defaultListingFields, ...rest } = defaultConfigs.listing || {};

  const shouldMerge = mergeDefaultTypesAndFieldsForDebugging(false);
  const listingTypes = shouldMerge
    ? union(hostedListingTypes, defaultListingTypes, 'listingType')
    : hostedListingTypes;
  const listingFields = shouldMerge
    ? union(hostedListingFields, defaultListingFields, 'key')
    : hostedListingFields;

  const listingTypesInUse = getListingTypeStringsInUse(listingTypes);

  return {
    ...rest,
    listingFields: validListingFields(listingFields, listingTypesInUse),
    listingTypes: validListingTypes(listingTypes),
    enforceValidListingType: defaultConfigs.listing.enforceValidListingType,
  };
};
```

By default, this function first determines the hosted and local listing fields and listing types. Then, it checks whether to merge both, or to only use hosted configurations.

### Example: use both hosted and local configs for listing types

Let's say you want to use multiple listing types in your marketplace – one for regular bookings, defined in Sharetribe Console, and one for [negotiated bookings](https://github.com/sharetribe/example-processes/tree/master/negotiated-booking), defined in the template.

First, follow the directions in the process README.md to configure the template to use the new process. Then, make the following modifications to the *mergeListingConfig* function:

```diff
const mergeListingConfig = (hostedConfig, defaultConfigs) => {
...
- const listingTypes = shouldMerge
-   ? union(hostedListingTypes, defaultListingTypes, 'listingType')
-   : hostedListingTypes;
+ const listingTypes = union(hostedListingTypes, defaultListingTypes, 'listingType');
...
};
```

With this change, you are still retrieving listing fields and other configurations primarily from hosted configurations, and using default configurations only as a fallback. However, listing types now combine both hosted asset and default config listing types.
```