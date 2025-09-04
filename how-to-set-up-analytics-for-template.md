```markdown
---
title: "Enable Analytics"
source: "provided"
extracted_date: "2023-10-24"
summary: "This guide describes how to enable analytics in the Sharetribe Web Template."
---

- [Overview](#overview)
- [Configure Google Analytics](#configure-google-analytics)
- [Google Analytics 4](#google-analytics-4)
- [Built-in handlers](#built-in-handlers)
- [Configure Plausible Analytics](#configure-plausible-analytics)
- [Custom analytics libraries](#custom-analytics-libraries)
- [Add the analytics library script](#add-the-analytics-library-script)
- [Create a handler](#create-a-handler)
- [Initialise the handler](#initialise-the-handler)

# Overview
The Sharetribe Web Template comes with built-in support for Google Analytics and supports tracking page views with a customizable analytics handler. The template also supports using Plausible analytics for tracking page views. This article explains how to enable Plausible or Google Analytics and use and create custom analytics handlers.

## Configure Google Analytics
The template has built-in support for Google Analytics. All you need to do is assign your Google Analytics Tracking ID to the environment variable `REACT_APP_GOOGLE_ANALYTICS_ID`.

> **Information**  
> Google Analytics doesn't work in a hot-loading environment! The analytics script is added server-side. You can test it in your local environment by using the command `yarn run dev-server`.

## Google Analytics 4
Google recently released their new analytics service Google Analytics 4. Support for Google Universal Analytics will end on October 1, 2023. New versions of the template provide out-of-the-box support for Google Analytics 4.

> **Information**  
> The template will require a Tracking ID compatible with Google Analytics 4. The ID needs to begin with the "G-" prefix.

### Enhanced measurements
It is not recommended to use the Enhanced Measurements feature introduced in Google Analytics 4, which is enabled by default. The Enhanced Measurements feature injects code into link tags which can break in-app navigation in the template. Therefore, we strongly recommend disabling the Enhanced Measurements feature when using Google Analytics 4 with the template.

If that's not an option, you can continue to use Enhanced Measurements if you disable the *Outbound clicks* and *page changes based on browser history events* features.

## Built-in handlers
The Sharetribe Web Template includes an [event handler](https://github.com/sharetribe/web-template/blob/main/src/analytics/handlers.js#L9) that sends `page_view` events to Google Analytics. These events need to be registered manually because the template is a single-page application, meaning that in-app navigation does not render a page load.

The Google Analytics script registers a `page_view` event automatically on every page load. The [trackPageView](https://github.com/sharetribe/web-template/blob/main/src/analytics/handlers.js#L9) function takes this into account and only sends a `page_view` event to Google if a page is accessed through in-app navigation.

If you'd like to track something other than page views, you can implement your custom handler using the `trackPageView` function as an example.

## Configure Plausible Analytics
The template also includes built-in support for [Plausible Analytics](https://plausible.io). Plausible is a GDPR compliant open-source web analytics service. You must assign the data-domain value from the Plausible Analytics tracking script to the environment variable `REACT_APP_PLAUSIBLE_DOMAINS`.

If this would be your Plausible analytics script:
```html
<script defer data-domain="example.com" src="https://plausible.io/js/script.js"></script>
```
You would assign `example.com` to the environment variable:
```bash
REACT_APP_PLAUSIBLE_DOMAINS=example.com
```
You can also assign multiple domains separated by a comma:
```bash
REACT_APP_PLAUSIBLE_DOMAINS=example1.com,example2.com
```

## Custom analytics libraries
If you choose to add another analytics provider (e.g., Facebook Pixel), you can follow these steps to import the third-party script and create a custom handler. In some cases, it might also be worth looking into npm packages instead of manually appending a third-party script.

### Add the analytics library script
If the analytics library has an external script, you can add the library script tag to the [src/util/includeScripts.js](https://github.com/sharetribe/web-template/blob/main/src/util/includeScripts.js) file. You will also need to whitelist the corresponding URLs in the [server/csp.js](https://github.com/sharetribe/web-template/blob/main/server/csp.js) file.

#### Example: Add Meta/Facebook Pixel tracking script
We suggest including third-party scripts via the [src/util/includeScripts.js](https://github.com/sharetribe/web-template/blob/main/src/util/includeScripts.js) file. You cannot add inline scripts without adding a nonce attribute. See [this pull request](https://github.com/sharetribe/web-template/pull/485) for more information on why nonce needs to be used with inline scripts. Therefore, the recommended approach is to load external scripts dynamically using the script tag.

As an example, we will use the Meta Pixel. You will first need to create a script file under `public/static/scripts/facebook/facebook.js` and add the Meta Pixel code. You can find the up-to-date base code under the [Meta Developer Documentation](https://developers.facebook.com/docs/meta-pixel/get-started/).

```javascript
(function (window, document, e, v, n, t, s) {
  if (window.fbq) return;

  (function (f, b, e, v, n, t, s) {
    if (f.fbq) return;
    n = f.fbq = function () {
      n.callMethod
        ? n.callMethod.apply(n, arguments)
        : n.queue.push(arguments);
    };
    if (!f._fbq) f._fbq = n;
    n.push = n;
    n.loaded = !0;
    n.version = '2.0';
    n.queue = [];
    t = b.createElement(e);
    t.async = !0;
    t.src = v;
    s = b.getElementsByTagName(e)[0];
    s.parentNode.insertBefore(t, s);
  })(window, document, 'script', 'https://connect.facebook.net/en_US/fbevents.js');

  fbq('init', '{your-pixel-id-goes-here}');
  fbq('track', 'PageView');

  const img = document.createElement('img');
  img.height = '1';
  img.width = '1';
  img.style.display = 'none';
  img.src = `https://www.facebook.com/tr?id={your-pixel-id-goes-here}&ev=PageView&noscript=1`;
  document.body.appendChild(img);
})();
```

Add that code to the `facebook.js` file. You can then import the file in [src/util/includeScripts.js](https://github.com/sharetribe/web-template/blob/main/src/util/includeScripts.js):

```javascript
analyticsLibraries.push(
  <script
    key="fb"
    src={`${rootURL}/static/scripts/facebook/facebook.js`}
  />
);
```

Remember to also update the [csp.js](https://github.com/sharetribe/web-template/blob/main/server/csp.js) file:

```javascript
const { imgSrc = [self] } = defaultDirectives;
const imgSrcOverride = imgSrc.concat('www.facebook.com');

const { scriptSrc = [self] } = defaultDirectives;
const scriptSrcOverride = scriptSrc.concat(
  'connect.facebook.net',
  'www.facebook.com',
);

const { frameSrc = [self] } = defaultDirectives;
const frameSrcOverride = frameSrc.concat(
  'connect.facebook.net',
  'www.facebook.com',
);

const { connectSrc = [self] } = defaultDirectives;
const connectSrcOverride = connectSrc.concat('www.facebook.com');

const customDirectives = {
  imgSrc: imgSrcOverride,
  scriptSrc: scriptSrcOverride,
  frameSrc: frameSrcOverride,
  connectSrc: connectSrcOverride,
};
```

### Create a handler
You can create a custom handler e.g. in [src/analytics/handlers.js](https://github.com/sharetribe/web-template/blob/main/src/analytics/handlers.js). If you want to track page views, you could create a class that implements a `trackPageView(canonicalPath, previousPath)` method.

Note that the `canonicalPath` parameter passed to the function might not be the same path as in the URL bar of the browser. It is the canonical form of the URL. 

**For example**: the listing page URL is constructed dynamically: `l/{listing-slug}/{listing-id}`. The canonical form of that URL would be: `l/{listing-id}`.

This approach allows unified analytics and correct tracking of pages that can be accessed from multiple URLs.

If your analytics library tries to access the page URL directly through the browser, you might need to override that behavior to use the canonical URL that is given to the method.

### Initialise the handler
Finally, you only need to initialise the handler in the `setupAnalyticsHandlers()` function in [src/index.js](https://github.com/sharetribe/web-template/blob/main/src/index.js).

---

## Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.

```