---
title: "Hosted marketplace texts with Asset Delivery API"
source: "provided"
extracted_date: "2025-01-09"
summary: "This article describes how hosted marketplace texts work in the Sharetribe Web Template."
---

- [Hosted marketplace texts](#hosted-marketplace-texts)
- [How production build works with hosted marketplace texts](#how-production-build-works-with-hosted-marketplace-texts)
- [How development build works with hosted marketplace texts](#how-development-build-works-with-hosted-marketplace-texts)
- [Read more](#read-more)

# Hosted marketplace texts with Asset Delivery API

This article describes how hosted marketplace texts work in the Sharetribe Web Template.

Marketplace texts can be managed both in the built-in marketplace text files and in Sharetribe Console. This article describes how the template uses the hosted marketplace texts and merges them with the built-in marketplace texts.

## Hosted marketplace texts

**Hosted marketplace texts** refer to the texts that the marketplace operator can edit in Sharetribe Console. The client app then needs to fetch it using the Asset Delivery API.

It is good to note that even if the operator adds some hosted marketplace texts using Sharetribe Console, the template still needs to have a built-in marketplace text file for the marketplace text keys that do not have a value in the hosted asset. That way, the UI can still render something meaningful for the parts of the page that the operator has not modified.

The template specifies the path to the hosted marketplace texts as part of the app-wide configuration in [*config/configDefault.js*](https://github.com/sharetribe/web-template/blob/main/src/config/configDefault.js#L78). These hosted marketplace texts live in a file called *content/translations.js*, since language-specific marketplace text files make it fairly easy to translate the template to languages other than the default English.

```js
// CDN assets for the app. Configurable through Sharetribe Console.
// Currently, only translation.json is available.
const appCdnAssets = {
  translations: 'content/translations.json',
};
```

In addition, the template has a global Redux file (*src/ducks/hostedAssets.duck.js*), which exports a Redux Thunk function called **fetchAppAssets**. This is the function that actually makes the calls to the Asset Delivery API.

There are two ways to fetch marketplace text assets using Asset Delivery API: by version or by alias.

#### Fetching marketplace texts by version

All assets are identifiable by their version, and versions are immutable. Therefore if you fetch assets by version, they can be cached for an extended period of time. Read more about [caching assets](/docs/references/assets/#asset-data-caching). When fetching marketplace texts by version, the result is cached for an extended period of time, which helps to avoid unnecessary data loading. Since Asset Delivery API sets Cache-Control header for these responses, the browser knows to cache these responses on its own.

Hosted assets are versioned as a [whole asset tree](/docs/references/assets/#asset-versioning) - a bit similar to how Git works. Individual asset files might have not changed when the whole version has changed and this might cause [HTTP redirects](https://www.sharetribe.com/api-reference/asset-delivery-api.html#http-redirects). Since the template uses Sharetribe SDK, the response always contains the data for the requested asset, even if the asset has not changed in the specified version.

```js
sdk.assetByVersion({
  path: 'content/translations.json',
  version: '<some-hash-string>',
});
```

#### Fetching marketplace texts by alias

In addition to fetching assets by version, you can fetch them by a specific alias instead of a version. Currently, the marketplace text asset can be fetched with the alias *latest*, which returns the most recently updated version of the marketplace text file. The response also contains the version information for the most recent asset, so that subsequent fetches can be done based on asset version.

When fetching by alias, the cache time is a few seconds for the Dev and Test environments and up to 5 minutes for the Live environment. In other words, it can take up to 5 minutes for marketplace text updates to be visible in a Live environment. These cache times are subject to change.

```js
sdk.assetByAlias({
  path: 'content/translations.json',
  alias: 'latest',
});
```

## How production build works with hosted marketplace texts

This setup is in use if you run ***yarn start*** in your host environment or ***yarn run dev-server*** on your local machine.

1. Export **fetchAppAssets** from *src/index.js* to make it available for the server.

2. SSR: initialize the store
   ```
   └── server
       └── dataLoader.js
   ```
   ```js
   let translations = {};
   const store = configureStore({}, sdk);
   return store.dispatch(fetchAppAssets(config.appCdnAssets));
   ```

3. SSR: **fetchAppAssets** thunk fetches the latest version of *content/translations.json* asset by using *latest* alias with **sdk.assetByAlias**
   - This ensures that the server-side rendering has the most recent version of asset to render the page

4. SSR: make a **loadData** call if the route specifies it and
   ```js
     .then(fetchedAssets => {
       translations = fetchedAssets?.translations?.data || {};
       return Promise.all(dataLoadingCalls);
   })
   ```

5. SSR: asset **version** is saved to the store and passed to the browser through the *preloadedState*.
   - Server-side rendering must match with client-side rendering when browser hydrates the server-side rendered content.
   - To ensure that client-side rendering has the same version of marketplace texts, the asset version is passed (through preloaded state) to front end.
   - Note: The template does not pass the marketplace text asset itself from the server to browser. The reason is that if browser fetches the versioned asset file directly, it can leverage browser's cache. So, every page load, after the initial one, will use the *translation.json* file from the browser's local cache.

   ```js
     .then(() => {
       return { preloadedState: store.getState(), translations };
     })
   ```

6. SSR: call **renderApp** function, which renders the <*ServerApp*> in *src/app.js*
   - Hosted marketplace text file from Asset Delivery API is passed as props to *ServerApp*.

   ```
   └── server
       └── renderer.js
   ```
   ```js
   // Render the app with given route, preloaded state, hosted microcopy.
    // Render the app with given route, preloaded state, hosted translations.
   return renderApp(
    requestUrl,
    context,
    preloadedState,
    translations,
    hostedConfig,
    collectWebChunks
   ).then(({ head, body }) => {
   ```
   ```
   └── src
       └── app.js
   ```
   ```js
   <ServerApp
     url={url}
     context={serverContext}
     helmetContext={helmetContext}
     store={store}
     hostedTranslations={hostedTranslations}
   />
   ```

- Hosted marketplace texts are then merged with the default marketplace texts in *ServerApp* component.
  ```jsx
  export const ServerApp = props => {
    const { url, context, helmetContext, store, hostedTranslations = {}, hostedConfig = {} } = props;
    const appConfig = mergeConfig(hostedConfig, defaultConfig);
    HelmetProvider.canUseDOM = false;
    return (
      <Configurations appConfig={appConfig}>
        <IntlProvider
          locale={appConfig.localization.locale}
          messages={{ ...localeMessages, ...hostedTranslations }}
          textComponent="span"
        >
    {/* etc. */}
  ```

7. Browser: initialize the store with a preloaded state
   - The asset version that the SSR used, is included in that preloaded state

8. Browser: **fetchAppAssets** thunk fetch assets using asset version: calls **sdk.assetByVersion**.

9. Browser: make **loadData** call

10. Browser: hydrate the *<ClientApp>* and pass marketplace texts as props.

- Hosted marketplace texts are merged with default marketplace texts in *ClientApp* component.
  ```js
  <ClientApp store={store} hostedTranslations={translations} hostedConfig={hoste