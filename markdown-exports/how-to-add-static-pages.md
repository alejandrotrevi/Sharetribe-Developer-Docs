```markdown
---
title: "How to add a static page"
source: "provided"
extracted_date: "2023-10-24"
summary: "This guide describes how to add a non-dynamic static page using the Sharetribe Web Template."
---

- [Overview](#overview)
- [Create a New Folder](#1-create-a-new-folder)
- [Create the Component](#2-create-the-component)
- [Import Components](#import-components)
- [Define Layout Areas](#define-layout-areas)
- [Add Page Schema](#add-page-schema)
- [Define Component Structure](#define-component-structure)
- [Style the Component](#style-the-component)
- [Add Routing](#add-routing)
- [Read More](#read-more)

# Overview
You can create new content pages through the Console using the Pages feature. However, sometimes you may need a static page that does not use dynamic content. This guide walks you through the steps required to create a static and non-dynamic content page, from where to create the new component to how to add new routes.

You might want to do this, for instance, if:
- you have a page that embeds a component that fetches its own data, or
- you want to optimize some pages for performance, so that they do not fetch any data.

## 1. Create a New Folder
Create a new folder under *src/containers/* with the name of your static page. E.g. if you are creating a page for embedding your social media feeds, it should be named **SocialMediaPage**.

Create a new JavaScript file using the folder name. The path should look like *src/containers/SocialMediaPage/SocialMediaPage.js*.

Create a new CSS file using the folder name. The path should look like *src/containers/SocialMediaPage/SocialMediaPage.module.css*.

## 2. Create the Component
Template for a single column static page (SocialMediaPage.js):

```jsx
import React from 'react';
import {
  NamedLink,
  ExternalLink,
  LayoutComposer,
  Heading,
} from '../../components';

import StaticPage from '../PageBuilder/StaticPage';
import TopbarContainer from '../TopbarContainer/TopbarContainer';
import FooterContainer from '../FooterContainer/FooterContainer';

import css from './SocialMediaPage.module.css';

const SocialMediaPage = () => {
  const layoutAreas = `
    topbar
    main
    footer
  `;

  return (
    <StaticPage
      className={css.root}
      title="Social media"
      schema={{
        '@context': 'http://schema.org',
        '@type': 'SocialMediaPage',
        description: 'Description of this page',
        name: 'Social media page',
      }}
    >
      <LayoutComposer areas={layoutAreas} className={css.layout}>
        {() => (
          <>
            <TopbarContainer />
            <div className={css.content}>
              {/* <!-- Add your page content here --> */}
              <Heading>See Biketribe in Social Media</Heading>
              <iframe
                width="560"
                height="315"
                src="https://www.youtube-nocookie.com/embed/FHk3pPfaZHA"
                title="YouTube video player"
              ></iframe>
              <NamedLink className={css.link} name="LandingPage">
                Go to our home page
              </NamedLink>
              or
              <ExternalLink className={css.link} href="https://google.com">
                Go to Google
              </ExternalLink>
            </div>
            <FooterContainer />
          </>
        )}
      </LayoutComposer>
    </StaticPage>
  );
};

export default SocialMediaPage;
```

We are using [React](https://reactjs.org/) and [JSX](https://reactjs.org/docs/introducing-jsx.html) to create components and pages. Therefore, we need to import React to our new component which is done in the first line.

```jsx
import React from 'react';
```

## Import Components
On the second line, we import some components:
- **NamedLink** makes it easier to point to different pages inside the application.
- **ExternalLink** can be used to link outside the application. It creates a normal `<a>` link with extra attributes `target="_blank" rel="noopener noreferrer"` that add some security to these outbound links.
- **LayoutComposer** wraps the page layout elements by creating container and area wrappers using CSS Grid Template Areas.
- **Heading** makes it easier to create headings with uniform style across the site.

```jsx
import {
  NamedLink,
  ExternalLink,
  LayoutComposer,
  Heading,
} from '../../components';
```

After that we import three containers:
- **StaticPage**: helps in creating static pages.
- **TopbarContainer**: creates our Topbar component and fetches the data it needs.
- **FooterContainer**: creates our Footer component and fetches the data it needs.

```jsx
import StaticPage from '../PageBuilder/StaticPage';
import TopbarContainer from '../TopbarContainer/TopbarContainer';
import FooterContainer from '../FooterContainer/FooterContainer';
```

Then we need to import styles and possible other files from the current folder. The template uses [CSS Modules](https://github.com/css-modules/css-modules) to scope all class names locally to prevent conflicts.

```jsx
import css from './SocialMediaPage.module.css';
```

## Define Layout Areas
First, we need to define layout areas that will be used in the **LayoutComposer** component. Since we will pass a topbar, a main component area, and a footer, let's name those areas accordingly.

```jsx
const layoutAreas = `
    topbar
    main
    footer
`;
```

## Add Page Schema
In the template above, we use the StaticPage component with some attributes:

```jsx
<StaticPage
  className={css.root}
  title="Social media"
  schema={{
    '@context': 'http://schema.org',
    '@type': 'CollectionPage',
    description: 'Description of this page',
    name: 'Social media page',
  }}
>
```

- `className` is JSX name for `class` attribute used in plain HTML.
- `title="Social media"` creates `<title>Social media</title>` element to `<head>` section of the page. (That title is also used in OpenGraph meta tags). You could also add `description="This is the description for the social media page"`.
- Then we have `schema` tag that defines some data for search engines in JSON-LD format. Check [schema.org](https://schema.org/docs/full.html) for more information. You can also review [Google's structured data types](https://developers.google.com/search/docs/appearance/structured-data/search-gallery) to see if one of them fits your use case.

## Define Component Structure
Inside the **StaticPage** component, we wrap the content into the LayoutComposer component, which expects a functional React component as children. Within that component, we pass three elements that correspond to the layout areas defined earlier:
- **TopBarContainer** for the *topbar* layout area,
- **div** with class *css.content* for the main layout area, and
- **FooterContainer** for the *footer* layout area.

Any static content we want to show on this page will be wrapped inside the *css.content* **div**.

```jsx
<LayoutComposer areas={layoutAreas} className={css.layout}>
```

## Style the Component
Here's an example what your *SocialMediaPage.module.css* file could look like:

```css
/**
 * Import custom media queries for the new page.
 * The template uses route-based code-splitting, every page create their own CSS files.
 * This import ensures that the page and components inside will get correct media queries,
 * when the app is built.
 */
@import '../../styles/customMediaQueries.css';

.content {
  margin: 0 auto;
  max-width: 784px;
}

.link {
  display: block;
  margin-top: 6px;
  margin-bottom: 6px;
}
```

## Add Routing
As a last step, you need to add the newly created static page to the routing. This can be done in *src/routing/routeConfiguration.js*.

Inside the *routeConfiguration* function, you should add a URL path, a page name (it should not conflict with other pages), and the component itself.

Add a new asynchronous import for the page in the beginning of the file with other page imports:

```javascript
const SocialMediaPage = loadable(() =>
  import(
    /* webpackChunkName: "SocialMediaPage" */ '../containers/SocialMediaPage/SocialMediaPage'
  )
);
```

After that, add the route configuration to your newly created page: (In this example, we created a social media page, so '/social-media' would work well as a path.)

```javascript
{
  path: '/social-media',
  name: 'SocialMediaPage',
  component: SocialMediaPage,
}
```

## Read More
We are using several libraries in this example. If you want to read more, here's some pointers:
- [ES2015](https://babeljs.io/docs/en/learn/): imports, exports, arrow functions
- [React](https://reactjs.org/): for creating components
- [JSX](https://reactjs.org/docs/introducing-jsx.html): for getting HTML-like markup syntax for own components
- [CSS Modules](https://github.com/css-modules/css-modules)
- [React Router](https://reacttraining.com/react-router/web/guides/philosophy): routing inside the application.
```