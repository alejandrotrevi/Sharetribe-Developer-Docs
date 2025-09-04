```markdown
---
title: "Bundled Marketplace Texts"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2023-10-24"
summary: "This guide describes what bundled marketplace texts are, and how to make changes to them."
---

- [The marketplace text file](#the-marketplace-text-file)
- [Using marketplace texts](#using-marketplace-texts)
- [Formatting](#formatting)
- [Arguments](#arguments)
- [Pluralization](#pluralization)
- [Select an option](#select-an-option)

# Bundled Marketplace Texts

This guide describes what bundled marketplace texts are, and how to make changes to them.

## The marketplace text file

All the bundled marketplace texts can be found in the [src/translations/en.json](https://github.com/sharetribe/web-template/blob/master/src/translations/en.json) file. The marketplace text data is formatted as one JSON object with all the marketplace text key-value pairs as properties.

The key - value syntax is as follows:

```json
"<component name>.<marketplace text key>": "<message>"
```

For example:

```json
"ManageListingCard.viewListing": "View listing"
```

The keys are namespaced to the corresponding component. This is aligned with the component-driven paradigm that the application follows. 

## Using Marketplace Texts

React Intl provides multiple ways to access the marketplace text data, but the most commonly used are the `formatMessage` function and the `FormattedMessage` tag provided by React Intl.

To use the `formatMessage` function, the component needs to be wrapped with the `injectIntl` function, which provides a render prop called `intl`. `intl` then provides all the React Intl translation functions, like `formatMessage`:

```javascript
import { injectIntl } from 'react-intl';

const SomeComponent = (props) => {
  const { intl } = props;

  const translation = intl.formatMessage({
    id: 'SomeComponent.someKey',
  });

  // ...
};

export default injectIntl(SomeComponent);
```

As for the `FormattedMessage`, it just needs to be imported from `react-intl` and it takes the id prop:

```jsx
<FormattedMessage id="SomeComponent.someKey" />
```

Other functions and components can be explored in the [React Intl documentation](https://github.com/yahoo/react-intl/wiki).

## Formatting

React Intl uses the [FormatJS](https://formatjs.io/) formatters for shaping the messages based on given arguments. Here are a few examples on how to use FormatJS.

### Arguments

Pass a named argument to the format function/component. For the following message:

```json
"EnquiryForm.messageLabel": "Message to {authorDisplayName}"
```

Pass the author data in the `FormattedMessage` component:

```jsx
<FormattedMessage
  id="EnquiryForm.messageLabel"
  values={{ authorDisplayName: 'Jane D' }}
/>
```

Or the `formatMessage` function:

```javascript
intl.formatMessage({
  id: 'EnquiryForm.messageLabel',
}, {
  authorDisplayName: 'Jane D'
});
```

### Pluralization

With pluralization, a message can be formatted to adapt to a number argument.

```json
"ManageListingsPage.youHaveListings": "You have {count} {count, plural, one {listing} other {listings}}"
```

This message takes the `count` argument and uses the `plural`, `one` and `other` keywords to format the last word of the message.

The pluralized message can be used with the `FormattedMessage` component:

```jsx
<FormattedMessage
  id="ManageListingsPage.youHaveListings"
  values={{ count: 3 }}
/>
```

Or with the `formatMessage` function:

```javascript
intl.formatMessage({
  id: 'ManageListingsPage.youHaveListings',
}, {
  count: 1
});
```

### Select an Option

If you have two or more options that the message needs to show depending on another argument, you can use the `select` keyword to pass the necessary information to the message.

When you use `select` in the message, you need to specify:

- the variable determining which option to use (here: `mode`)
- the pattern we are following (here: `select`)
- the options matching each alternative you want to specify (here: `class`)
- an `other` option that gets used when none of the specified alternatives matches.

```json
"BookingBreakdown.description": "{mode, select, day {You are booking the following days:} night {You are booking the following nights:} other {You are booking the following {unitType}:}}"
```

You can then use the message in the code with the `formatMessage` function:

```javascript
const mode = 'class';
const unitType = 'yoga class';
// For { mode: 'class', unitType: 'yoga class' }, the message will read "You are booking the following yoga class."
const description = intl.formatMessage(
  { id: 'BookingBreakdown.description' },
  { mode, unitType }
);
```

You can also use the message with the `FormattedMessage` component:

```jsx
<FormattedMessage
  id="BookingBreakdown.description"
  values={{ mode: 'day' }}
/>
```

More formatting examples can be found from the [FormatJS message syntax documentation](https://formatjs.io/docs/core-concepts/icu-syntax/).
```