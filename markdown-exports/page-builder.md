---
title: "How the template renders content pages"
source: "provided"
extracted_date: "2025-01-13"
summary: "This article introduces how the Sharetribe Web Template uses the Pages feature to generate content pages."
---

- [What are content pages](#what-are-content-pages)
- [Page Asset Data](#page-asset-data)
- [Rendering pages](#rendering-pages)
  - [Routing and loadData calls](#routing-and-loaddata-calls)
  - [Predefined routes](#predefined-routes)
  - [PageBuilder](#pagebuilder)
- [Section and Block types](#section-and-block-types)
- [Fallback pages](#fallback-pages)
- [Maintenance mode](#maintenance-mode)
- [How to take Pages into use if you are using a legacy template](#how-to-take-pages-into-use-if-you-are-using-a-legacy-template)

# How the template renders content pages

This article introduces how the Sharetribe Web Template uses the Pages feature to generate content pages.

The Pages feature allows you to add, edit and manage content in Sharetribe Console. Once you have created content in Console, you can query it through the Asset Delivery API, which returns the data structured as JSON. The Sharetribe Web Template contains features that automatically render content pages from Page Asset Data. This article will walk you through the logic used to render these pages in the template.

> **I am using a legacy template without Pages support**
>
> Read more about the code-level changes introduced in the legacy FTW templates in the release notes of [version 10.0.0](https://github.com/sharetribe/ftw-daily/releases/tag/v10.0.0).
>
> You can find instructions on adding the Pages capability into your legacy template [in our legacy documentation](https://www.sharetribe.com/docs/template/legacy-templates/).

## What are content pages

A content page is a page type that is informational by nature with text that does not frequently change. Typical content pages that you would have on your website would be an "About us" page, a "Frequently asked questions" page or a "Terms of Service" page. These pages have long sections of written text that might include images, links and videos.

In older versions of our [legacy templates](https://www.sharetribe.com/docs/template/legacy-templates/), the data on these content pages was hard-coded into the corresponding page file. For instance, the content of the About Page was [written directly into the code](https://github.com/sharetribe/ftw-daily/blob/7a2f9b0557607533097761c063f7f98d7c8bfc1a/src/containers/AboutPage/AboutPage.js) on the AboutPage.js file. Changes to the content required editing the code and redeploying the client application. This required content editors to work with developers to make simple changes to the copy text of content pages.

Sharetribe Web Template renders content pages dynamically. Content can be managed through the Pages feature, which provides the editor with a graphical interface to input text, videos, links and images. The template queries the [Asset Delivery API](https://www.sharetribe.com/api-reference/asset-delivery-api.html) to retrieve the most recent version of the content and uses it to render the content page. We refer to this data as [Page Asset Data](#page-asset-data). It reflects the content's structure and is delivered in JSON.

On page load, the template queries the Asset Delivery API to fetch the Page Asset Data needed to render the requested page. The data is subsequently stored in Redux state, which triggers a component called the PageBuilder to render the Sections, Blocks and Fields defined in the data. The [rendering pages section](#rendering-pages) explains how this happens in further detail.

## Page Asset Data

Page Asset Data is a machine-readable format of the data inputted through Pages in Console. It represents the content and structure of the content page and is divided into Sections, Blocks and Fields.

A single query to the Asset Delivery API will provide you with the Page Asset Data of a single content page. In other words, to render both your landing page and your FAQ page, the client will need to make two calls to the Asset Delivery API and receive two separate JSON files. Page Asset Data is always formatted in JSON.

Page Asset Data nests 3 levels of information:

- The Page Asset, which represents all data associated with an individual page
- The Page Asset can contain an array of Sections. Sections can have a type, and there are 4 different types available by default.
- Sections can contain an array of Blocks. Blocks can include text formatted in markdown.

The structure outlined above is hierarchical: Blocks are always nested within Sections, and Sections are always nested within the Page Asset. Both Sections and Blocks may include Fields, which are key-value pairs encoding data such as title, ingress and background image.

- Read more: [Page asset schema](/docs/references/page-asset-schema/)

It is up to the client application how it renders the data received through the Asset Delivery API. Identical Page Asset Data can, for example, be rendered using entirely different visual elements on two different client applications.

## Rendering pages

### Routing and loadData calls

Sharetribe Web Template uses React Router to [create routes](/docs/template/how-routing-works-in-template/) to different pages. This is best demonstrated through an example. When a user navigates to the about page, it triggers the loadData function specified in [routeConfiguration.js](https://github.com/sharetribe/web-template/blob/main/src/routing/routeConfiguration.js#L76):

```js
{
   path: '/privacy-policy',
   name: 'PrivacyPolicyPage',
   component: PrivacyPolicyPage,
   loadData: pageDataLoadingAPI.PrivacyPolicyPage.loadData,
 },
```

In [legacy templates](https://www.sharetribe.com/docs/template/legacy-templates/), no loadData function was defined for the privacy policy path, as the page's content was hard coded. Now, as the content of the page is fetched using an API call, a loadData function is specified in [PrivacyPolicyPage.duck.js](https://github.com/sharetribe/web-template/blob/main/src/containers/PrivacyPolicyPage/PrivacyPolicyPage.duck.js):

```js
export const loadData = (params, search) => dispatch => {
  const pageAsset = {
    privacyPolicy: `content/pages/${ASSET_NAME}.json`,
  };
  return dispatch(fetchPageAssets(pageAsset, true));
};
```

The function uses the fetchPageAssets function to fetch the Page Asset Data for the privacy policy page. Once the data is loaded and stored in state, the page can be fully rendered using the data stored in Page Assets.

### Predefined routes

Sharetribe Web Template has four predefined routes used to generate content pages:

- [PrivacyPolicy](https://github.com/sharetribe/web-template/blob/main/src/containers/PrivacyPolicyPage/PrivacyPolicyPage.js)
- [TermsOfService](https://github.com/sharetribe/web-template/blob/main/src/containers/TermsOfServicePage/TermsOfServicePage.js)
- [LandingPage](https://github.com/sharetribe/web-template/blob/main/src/containers/LandingPage/LandingPage.js)
- [CMSPage](https://github.com/sharetribe/web-template/blob/main/src/containers/CMSPage/CMSPage.js)

The first three are defined by default in Console and can not be removed. Therefore, there is a dedicated component in the template for each. For any new page created through Console, a generic component called CMSPage is used.

If we compare the loadData calls in the privacy policy page and CMSPage, we can see that they differ slightly. The PrivacyPolicyPage.duck.js file uses a predefined path for fetching the page asset `content/pages/privacy-policy`

```js
import { fetchPageAssets } from '../../ducks/hostedAssets.duck';
export const ASSET_NAME = 'privacy-policy';

export const loadData = (params, search) => dispatch => {
  const pageAsset = {
    privacyPolicy: `content/pages/${ASSET_NAME}.json`,
  };
  return dispatch(fetchPageAssets(pageAsset, true));
};
```

Whereas the CMSPage uses a dynamic ID that is