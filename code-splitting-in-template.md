```markdown
---
title: "Code splitting"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2023-10-24"
summary: "This article explains how code splitting works in the Sharetribe Web Template."
---

- [What is code splitting](#what-is-code-splitting)
- [Why should you use it?](#why-should-you-use-it)
- [How code splitting works in practice](#how-code-splitting-works-in-practice)
- [Preloading code chunks](#preloading-code-chunks)
- [Preloadable components in route configuration](#preloadable-components-in-route-configuration)
- [CSS chunk changes](#css-chunk-changes)
- [Server-side rendering (SSR)](#server-side-rendering-ssr)
- [Build directory](#build-directory)

# Code splitting

This article explains how code splitting works in the Sharetribe Web Template.

## What is code splitting

Instead of downloading the entire app before users can use it, code splitting allows us to split code away from one main.bundle.js file into smaller chunks which you can then load on demand. To familiarize yourself with the subject, you could read about code splitting from [reactjs.org](https://reactjs.org/docs/code-splitting.html).

In practice, the template uses route-based code splitting: page-level components use the [Loadable Components](https://loadable-components.com/) syntax to create [dynamic imports](https://webpack.js.org/api/module-methods/#import-1).

```javascript
const AboutPage = loadable(() =>
  import(
    /* webpackChunkName: "AboutPage" */ './containers/AboutPage/AboutPage'
  )
);
```

When Webpack comes across these loadable objects, it will create new JS & CSS chunk files (e.g. *AboutPage.dc3102d3.chunk.js*). Those code-paths are separated from the main bundle.

## Why should you use it?

The main benefit of code splitting is reducing the code loaded for any single page. That improves performance, but even more importantly, it makes it possible to add more navigational paths and page variants to the codebase. For example, adding different kinds of ListingPages for different listings makes more sense with code-splitting. Without code splitting, new pages, features, and libraries would impact the app's initial page load, and therefore SEO performance would drop too.

## How code splitting works in practice

Open the `/about` page. You will notice several JavaScript and CSS files loading:

- **Main chunk** (e.g. *main.1df6bb19.chunk.js* & main.af610ce4.chunk.css). They contain code that is shared between different pages.
- [**Vendor chunk**](https://twitter.com/wSokra/status/969633336732905474) (Currently, it's an unnamed chunk file. e.g. *24.230845cc.chunk.js*)
- **Page-specific chunk** (e.g. *AboutPage.dc3102d3.chunk.js*)

There are several chunk files that can be loaded in parallel in the first page-load and also page-specific chunks that can be loaded in response to in-app navigation. This means that during in-app navigation, the app needs to load data that the next page needs and a code chunk that it needs to render the data. The latter is not needed if the page-specific chunk is already loaded.

### Preloading code chunks

Route-based code splitting means that there might be a fast flickering of a blank page when navigation happens for the first time to a new page. To remedy that situation, the template preloads page-chunks when the mouse is over **NamedLink**. Additionally, **Form** and **Button** components can have a property `enforcePagePreloadFor`. This way the specified chunk is loaded before the user has actually clicked the button or executed form submit.

### Preloadable components in route configuration

Preloadable components are defined in the `routeConfigurations.js` file:

```plaintext
└── src
    └── routing
        └── routeConfiguration.js
```

At the beginning of the file, you can find the loadable component assigned to a constant variable that is assigned to the `component` property of the corresponding route configuration:

```javascript
// const AuthenticationPage = loadable(() => import(/* webpackChunkName: "AuthenticationPage" */ './containers/AuthenticationPage/AuthenticationPage'));
{
  path: '/signup',
  name: 'SignupPage',
  component: AuthenticationPage,
  extraProps: { tab: 'signup' },
},
```

#### Data loading

The Sharetribe Web Template collects `loadData` and `setInitialValues` Redux functions from a [modular Redux file](https://github.com/erikras/ducks-modular-redux) (i.e. files that look like `<SomePageComponent>.duck.js). This happens in `pageDataLoadingAPI.js`:

```plaintext
└── src
    └── containers
        └── pageDataLoadingAPI.js
```

Then those files can be connected with routing through route configuration.

```javascript
// import getPageDataLoadingAPI from './containers/pageDataLoadingAPI';
// const pageDataLoadingAPI = getPageDataLoadingAPI();
{
  path: '/l/:slug/:id',
  name: 'ListingPage',
  component: ListingPage,
  loadData: pageDataLoadingAPI.ListingPage.loadData,
},
```

### CSS chunk changes

To ensure that every page-level CSS chunk has custom media queries included, those breakpoints are included through a separate file (*customMediaQueries.css*) and it is imported into the main stylesheet of every page.

```plaintext
└── src
    └── styles
        └── customMediaQueries.css
```

## Server-side rendering (SSR)

When the template receives a page-load call on server and the page is a public one (i.e. the "auth" flag is not set in route configuration), the server will render the page into a string and returns it among HTML code. This process has 4 main steps:

1. *server/dataLoader.js* initializes the store
2. It also figures out which route is used and fetches route configuration for it
3. If the configuration contains a `loadData` function, the call is dispatched
4. As a consequence, the store gets populated and can be used to render the app to a string.

### Build directory

The sharetribe-scripts dependency uses Webpack to build the application. It copies the content from the *public/* directory into the *build* directory and bundles all the code into files for production mode.

- Code for server-side rendering is saved to *build/node* directory.
- Code for client-side rendering is saved to *build/static* directory.
- Both builds have also a *loadable-stats.json* file, which tells what assets different pages need.
- *server/importer.js* exposes two [ChunkExtractors](https://loadable-components.com/docs/server-side-rendering/#collecting-chunks) - one for web and another for node.
- *server/index.js* requires the entry point for the node build, extracts relevant info, and passes them to *dataLoader.loadData()* and *rendered.render()* calls.
- webExtractor (ChunkExtractor for the web build) is used to collect those different code chunks (JS & CSS files) that the current page-load needs.

In practice, the **renderApp** function wraps the app with `webExtractor.collectChunks`. This way, the webExtractor can figure out all the relevant loadable calls that the server uses for the current page and therefore the web-versions of those chunks can be included to rendered pages through `<script>` tags.

---

Can't find what you're looking for? Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at the [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```