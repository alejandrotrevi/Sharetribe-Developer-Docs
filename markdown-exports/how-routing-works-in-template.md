```markdown
---
title: "Routing in Sharetribe Web Template"
source: "provided"
extracted_date: "2023-10-24"
summary: "This article explains how routing works in the Sharetribe Web Template."
---

- [Overview](#overview)
- [Table of Contents](#table-of-contents)
- [React Router setup](#react-router-setup)
- [Route configuration](#route-configuration)
- [How the Sharetribe Web Template renders a route with routeConfiguration.js](#how-the-sharetribe-web-template-renders-a-route-with-routeconfigurationjs)
- [Linking](#linking)
- [NamedLink and NamedRedirect](#namedlink-and-namedredirect)
- [ExternalLink](#externallink)
- [Loading data](#loading-data)
- [Loading the code that renders a new page](#loading-the-code-that-renders-a-new-page)
- [Analytics](#analytics)
- [A brief introduction to SSR](#a-brief-introduction-to-ssr)

# Overview
This article explains how routing works in the Sharetribe Web Template.

## Table of Contents
1. [React Router setup](#react-router-setup)
2. [Route configuration](#route-configuration)
3. [How the Sharetribe Web Template renders a route with routeConfiguration.js](#how-the-sharetribe-web-template-renders-a-route-with-routeconfigurationjs)
4. [Linking](#linking)
5. [NamedLink and NamedRedirect](#namedlink-and-namedredirect)
6. [ExternalLink](#externallink)
7. [Loading data](#loading-data)
8. [Loading the code that renders a new page](#loading-the-code-that-renders-a-new-page)
9. [Analytics](#analytics)
10. [A brief introduction to SSR](#a-brief-introduction-to-ssr)

## React Router setup
The Sharetribe Web Template uses [React Router](https://reacttraining.com/react-router/web) for creating routes to different pages.

## Route configuration
The template's routing setup is simple. There is just one file to check before you link to existing routes or start creating new routes to static pages: *routeConfiguration.js*.

This page imports all the page-level components dynamically using [Loadable Components](https://loadable-components.com/). In addition, there's a configuration that specifies all the pages that are currently used within the template:

```
└── src
    └── routing
        ├── routeConfiguration.js
        └── Routes.js
```

```javascript
const AboutPage = loadable(() =>
  import(/* webpackChunkName: "AboutPage" */ './containers/AboutPage/AboutPage')
);
const AuthenticationPage = loadable(() =>
  import(/* webpackChunkName: "AuthenticationPage" */ './containers/AuthenticationPage/AuthenticationPage')
);
// etc..

// Our routes are exact by default.
// See behaviour from Routes.js where Route is created.
const routeConfiguration = () => {
  return [
    {
      path: '/about',
      name: 'AboutPage',
      component: AboutPage,
    },
    {
      path: '/login',
      name: 'LoginPage',
      component: AuthenticationPage,
      extraProps: { tab: 'login' },
    },
    {
      path: '/signup',
      name: 'SignupPage',
      component: AuthenticationPage,
      extraProps: { tab: 'signup' },
    },
    //...
  ];
};

export default routeConfiguration;
```

## How the Sharetribe Web Template renders a route with routeConfiguration.js
The route configuration is used in *src/app.js*. For example, **ClientApp** defines **BrowserRouter** and gives it a child component (**Routes**) that gets the configuration as `routes` property.

Here's a simplified *app.js* code that renders the client-side app:

```javascript
import { BrowserRouter } from 'react-router-dom';
import Routes from './Routes';
import routeConfiguration from './routeConfiguration';
//...

export const ClientApp = (props) => {
  return (
    <BrowserRouter>
      <Routes routes={routeConfiguration()} />
    </BrowserRouter>
  );
};
```

*Routes.js* renders the navigational **Route** components. **Switch** component renders the first *Route* that matches the location.

```javascript
import { Switch, Route } from 'react-router-dom';
//...

const Routes = (props, context) => {
  //...
  return (
    <Switch>
      {routes.map(toRouteComponent)}
      <Route component={NotFoundPage} />
    </Switch>
  );
};
```

Inside *Routes.js*, we also have a component called *RouteComponentRenderer*, which has four important jobs:
- Calling loadData function, if those have been defined in *src/routeConfiguration.js*. This is an asynchronous call; a page needs to define what gets rendered before data is complete.
- Reset scroll position after location change.
- Dispatch location changed actions to Redux store. This makes it possible for analytics Redux middleware to listen to location changes. For more information, see the [Enable analytics](https://www.example.com) guide.
- Rendering of the page-level component that the Route is connected through the configuration. Those page-level components are Loadable Components. When a page is rendered for the first time, the code-chunk for that page needs to be fetched first.

## Linking
Linking needs special handling in a single-page application (SPA). Using HTML `<a>` tags will cause the browser to redirect the user to the given **"href"** location. That will cause all resources to be fetched again, which is a slow and unnecessary step for a SPA.

## NamedLink and NamedRedirect
React Router exports a couple of [navigational components](https://reacttraining.com/react-router/web/api/Link) (e.g. `<Link to="/about">About</Link>`) that could be used for linking to different internal paths.

This is the reason why we have created names for different routes in *src/routeConfiguration.js*. We have a component called `<NamedLink name="LoginPage" />`.

Here is a more complex example of *NamedLink*:

```javascript
// Link to LoginPage:
<NamedLink name="LoginPage" />log in</NamedLink>

// Link to ListingPage with path `l/<listing-uuid>/<listing-title-as-url-slug>/`:
<NamedLink name="ListingPage" params={{ id: '<listing-uuid>', slug: '<listing-title-as-url-slug>' }}>
  some listing
</NamedLink>

// Link to SearchPage with query parameter: bounds
<NamedLink name="SearchPage" to={{ search: '?bounds=60.53,22.38,60.33,22.06' }}>
  Turku city
</NamedLink>
```

## ExternalLink
There’s also a component for external links. The **ExternalLink** component has safety measures to prevent security issues that can arise when linking to external resources.

```javascript
// Bad pattern: <a href="externalsite.com">External site</a>
// Recommended pattern:
<ExternalLink href="externalsite.com">External site</ExternalLink>
```

## Loading data
If a page component needs to fetch data, it can be done as a part of navigation. A page-level component has a related modular Redux file with a naming pattern: `PageName.duck.js`.

## Loading the code that renders a new page
The template uses route-based code splitting. Different pages are split away from the main code bundle and those page-specific code chunks are loaded separately when the user navigates to a new page for the first time.

## Analytics
It is possible to track page views to gather information about navigation behaviour. Tracking is tied to routing through *Routes.js* where *RouteRendererComponent* dispatches `LOCATION_CHANGED` actions.

## A brief introduction to SSR
Routing configuration is one of the key files to render any page on the server without duplicating routing logic. We just need to fetch data if `loadData` is defined on page component.
```