---
title: "How to implement a like feature using events"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2025-01-05"
summary: "A guide to building a feature that allows users to like listings using events and user extended data in the Sharetribe platform."
---

- [Approaches to updating extended data](#approaches-to-updating-extended-data)
- [Create a UI component the user can interact with](#create-a-ui-component-the-user-can-interact-with)
- [Update user extended data](#update-user-extended-data)
- [Update likes when clicking on the icon](#update-likes-when-clicking-on-the-icon)
- [Listening to events](#listening-to-events)
- [Increment the counter in the UI](#increment-the-counter-in-the-ui)

# How to implement a like feature using events

Following this guide, you'll be able to build a feature that allows users to like listings using events and user extended data

Events represent changes in marketplace data resources. In this guide, we use events to listen to changes in user extended data. We react to these events by updating a 'likes' value in listing extended data. In addition, we display the number of likes on the listing page and allow users to interact by clicking on a 'like' -button.

## Approaches to updating extended data

[Extended data](https://www.sharetribe.com/docs/references/extended-data/) is a practical feature that can be used to store structured data associated with either listings, users or transactions.

When implementing a like-counter, it's logical to store the number of likes associated with a listing in the listing's extended data. We can easily access the number of likes by querying the [query-listings](https://www.sharetribe.com/api-reference/marketplace.html#query-listings) endpoint and passing the relevant listing ID as a query parameter. However, merely storing the number of likes in the listing's extended data doesn't provide information about which user has liked the listing. If we can associate likes with users, we can build a dislike feature and show the user a list of listings they have liked. A naive approach would be to store the user ID in the listing's extended data each time they like a listing. However, to render a list of liked listings, we would need to loop through all listings to find all occurrences of the user ID. That is why in this guide, in addition to storing the number of likes in the listing's extended data, we choose to save the listing ID in the user's extended data.

To allow users to like listings, we introduce a UI-element users can interact with to like a listing. However, when we update a listing's extended data as a reaction to user input, we are prone to a race condition. Fortunately, Sharetribe provides a powerful feature called [events](https://www.sharetribe.com/docs/references/events/) that we can use to solve the problem. We can listen to events using a script that polls the [events endpoint](https://www.sharetribe.com/api-reference/integration.html#query-events). As events are handled sequentially, the script can update the listing's extended data while avoiding race conditions. A downside to using events is that the like count may take a moment to update. We can mask this delay by temporarily incrementintg the like value in the UI.

## Create a UI component the user can interact with

We start by creating an icon that users can interact with to like or dislike a listing. Next to the icon, we'll display the number of likes. For the UI component, we'll create a new subcomponent *SectionLikes.js* in the *ListingPage* directory.

### Step 1: Create a new file

The Sharetribe Web Template features two different listing page layouts. In this tutorial, we will implement the like feature to the default ListingPageCarousel version, but you can just as well follow the instructions to implement it in ListingPageCoverPhoto as well.

```shell
└── src
    └── containers
        └── ListingPage
            ├── SectionLikes.js
            ├── ListingPageCarousel.js
            └── ListingPage.module.css
```

### Step 2: Add the new subcomponent

We'll create a new component that renders an svg and the amount of likes:

```jsx
import React from 'react';
import { FormattedMessage } from '../../util/reactIntl';
import classNames from 'classnames';

import css from './ListingPage.module.css';

const IconHeart = () => {
  return (
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width="18"
      height="18"
      viewBox="0 0 24 28 "
    >
      <path d="M12 4.248c-3.148-5.402-12-3.825-12 2.944 0 4.661 5.571 9.427 12 15.808 6.43-6.381 12-11.147 12-15.808 0-6.792-8.875-8.306-12-2.944z" />
    </svg>
  );
};

const SectionLikes = props => {
  const { publicData } = props;

  const likes = publicData?.likes ? publicData.likes : 0;

  return (
    <span className={css.heartIcon}>
      <IconHeart /> {likes}
    </span>
  );
};

export default SectionLikes;
```

### Step 3: Import the component

Next, let's import the new component in *ListingPageCarousel.js*:

```jsx
import SectionLikes from './SectionLikes';
```

### Step 4: Create an instance of the component into a variable

We want to show the like button next to the listing title. In the template, where that title is shown depends on whether the listing page is viewed on mobile or on desktop. Because of this, we want to set the whole instance into a constant, so we can pass it to the correct contexts.

```diff
+  const sectionLikes = (
+    <SectionLikes
+      publicData={currentListing?.attributes?.publicData}
+    />
+  )
```

### Step 5: Pass the component variable to the render method and OrderPanel

We've created the new component *SectionLikes.js*, but it still needs to be included in the render method of *ListingPageCarousel.js* to show it in a mobile layout:

```diff
   <div className={css.mobileHeading}>
     <h1 className={css.orderPanelTitle}>
       <FormattedMessage id="ListingPage.orderTitle" values={{ title: richTitle }} />
     </h1>
+     {sectionLikes}
   </div>
```

To show the like component on a desktop layout, we need to pass it as a prop to OrderPanel.

```diff
   <div className={css.orderColumnForProductLayout}>
   <OrderPanel
     className={css.productOrderPanel}
     listing={currentListing}
     ...
+     sectionLikes={sectionLikes}
   />
```

### Step 6: Render the like section in OrderPanel.js

To show the like component in OrderPanel, we need to pick it from props and show it next to the title.

```shell
└── src
    └── components
        └── OrderPanel
            └── OrderPanel.js
```

Since we are passing the full instance as a prop, you can show it as a part of the heading.

```diff
const OrderPanel = props => {
  const {
    rootClassName,
    className,
    ...
+   sectionLikes,
  } = props;
...
   <div className={css.orderHeading}>
     {titleDesktop ? titleDesktop : <h2 className={titleClasses}>{title}</h2>}
     {subTitleText ? <div className={css.orderHelp}>{subTitleText}</div> : null}
+     {sectionLikes}
   </div>
```

### Step 7: Update ListingPage.module.css

Finally, let's style our new component by adding the following CSS rules in *ListingPage.module.css*:

```css
.heartIcon {
  border-radius: 11px;
  cursor: pointer;
  display: inline-block;
  padding: 5px;
  &:hover {
    background-color: #e3e1e1;
  }
}

.heartDisabled > svg {
  fill: var(--marketplaceColorLight);
}

.heartIcon > svg {
  fill: #fdb7b0