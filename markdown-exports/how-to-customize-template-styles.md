```markdown
---
title: "Styling and CSS"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2024-05-16"
summary: "This guide describes the CSS architecture used in the Sharetribe Web Template and how to apply changes to the styling using CSS."
---

- [Table of Contents](#table-of-contents)
  - [Marketplace level styling](#marketplace-level-styling)
  - [customMediaQueries.css](#custommediaqueriescss)
  - [marketplaceDefaults.css](#marketplacedefaultscss)
  - [Global CSS classes](#global-css-classes)
  - [Fonts](#fonts)
  - [Styling components](#styling-components)
  - [Finding a component](#finding-a-component)
  - [Styling guidelines](#styling-guidelines)
  - [Parent vs child](#parent-vs-child)
  - [Using vanilla CSS](#using-vanilla-css)

# Styling and CSS
This guide describes the CSS architecture used in the Sharetribe Web Template and how to apply changes to the styling using CSS.

## Marketplace level styling
Marketplace-level styling refers to global styles used across the template. The stylesheets presented in this section contain variables such as `marketplaceColor`, which defines the primary colour used in many icons and fonts across the marketplace.

```
└── src
    └── styles
        ├── customMediaQueries.css
        └── marketplaceDefaults.css
```

We have created marketplace-level styling variables with [CSS Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/--*) (vars) and a few global CSS classes.

### Example of CSS Properties

```css
/* in src/styles/marketplaceDefaults.css */
:root {
  --marketplaceColor: #ffff00;
}
```

### Usage in Components

```css
/* in component.module.css */
.linkToHomePage {
  color: var(--marketplaceColor);
}
```

Read more about CSS Properties from [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties).

## customMediaQueries.css
Breakpoints for media queries are defined in `customMediaQueries.css`.

```css
@custom-media --viewportSmall (min-width: 550px);
@custom-media --viewportMedium (min-width: 768px);
@custom-media --viewportLarge (min-width: 1024px);
/* etc. */
```

These custom media query breakpoints can be used in a similar way as CSS Properties. However, these variables are converted to real media queries during build-time.

```css
@media (--viewportMedium) {
  /* CSS classes */
}
```

On a live site, the CSS file contains:

```css
@media (min-width: 768px) {
  /* CSS classes */
}
```

## marketplaceDefaults.css
This is a good place to start customizing marketplace styles. For example, we define our color scheme here using [CSS Property](https://developer.mozilla.org/en-US/docs/Web/CSS/--*) variables:

```css
/* ================ Colors ================ */
--colorSuccess: #2ecc71;
--colorFail: #ff0000;
--colorAttention: #ffaa00;
/* etc. */
```

The variable `--colorSuccess` (green) is used in form inputs for showing that the input value is valid.

## Global CSS classes
Fonts and some other shared styles are specified in the `marketplaceDefaults.css` file using global (vanilla) CSS classes. They provide us a way to share some generic styles between components.

### Example of Default Font Styles

```css
.marketplaceDefaultFontStyles {
  font-family: var(--fontFamily);
  font-size: 14px;
  line-height: 24px;
}
```

This class can be used inside a component's [CSS Module syntax](#styling-components):

```css
.tip {
  composes: marketplaceDefaultFontStyles from global;
}
```

## Fonts
You can find all font-related rule sets from the [marketplaceDefaults.css](https://github.com/sharetribe/web-template/blob/main/src/styles/marketplaceDefaults.css) file. The default font is defined in the `--fontFamily` [CSS variable](https://developer.mozilla.org/en-US/docs/Web/CSS/--*).

## Styling components
Styling a web UI is traditionally quite a messy business due to the global nature of stylesheets. Our goal has been to create independent components that can be reused in the UI without paying too much attention to the global CSS context. To achieve this, we have used [CSS Modules](https://github.com/css-modules/css-modules).

### Example of Component Usage

```jsx
import css from './SectionHero.module.css';

const SectionHero = () => {
  return (
    <h1 className={css.heroMainTitle}>Book saunas everywhere</h1>
  );
};
```

## Finding a component
To pinpoint a component, open the inspector from your browser’s dev tools. Right-click on the correct element, and select `Inspector`.

![Finding component example](https://link-to-image.png)

## Styling guidelines
We have a practice of naming the outermost class of a component as `.root { /* styles */ }`. Some guidelines we follow include:

- Use semantic class names for readability.
- Use the CSS Properties defined in `marketplaceDefaults.css`.
- Use classes instead of styling DOM elements directly.
- Align text to a horizontal baseline (6px on mobile, 8px on larger layouts).

## Parent vs child
In a parent-child context, the parent component is responsible for its layout, including giving dimensions and margins to its child components. 

### Example of Parent-Child Component Interaction

```jsx
<div className={css.root}>
  <Circle className={css.circleDimensions} />
</div>
```

## Using vanilla CSS
The Sharetribe Web Template does not use vanilla CSS, but you can create CSS files without the `*.module.css` pattern to use vanilla CSS.

Read more about using vanilla CSS from [Create React App's docs](https://create-react-app.dev/docs/adding-a-stylesheet).
```