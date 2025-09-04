```markdown
---
title: "Change template texts"
source: "provided"
extracted_date: "2023-10-24"
summary: "This guide gives an overview how to change the user interface texts in the Sharetribe Web Template."
---

- [Marketplace texts](#marketplace-texts)
- [Content pages](#content-pages)
- [Static pages](#static-pages)
- [Labels and countries](#labels-and-countries)

# Change template texts

This guide gives an overview how to change the user interface texts in the Sharetribe Web Template.

## Marketplace texts

In the Sharetribe Web Template, user-facing content is not written directly into the source code. Instead, the source code uses [React Intl message formatting](https://formatjs.io/docs/intl#formatmessage) that defines keys for each meaningful piece of content, and a translator or a content creator can then define the message (i.e. the value) for each key in their language. Read more about how [Sharetribe handles marketplace texts](https://www.sharetribe.com/docs/concepts/marketplace-texts/).

By default, the template uses built-in language-specific marketplace text files to show messages in the UI. However, starting in 2022-05, operators can also modify marketplace texts in Sharetribe Console using hosted marketplace text assets. The built-in marketplace texts are merged with the hosted marketplace texts in the template, so you can use both ways of managing marketplace texts. Read more about how to [modify built-in marketplace texts in the template](https://www.sharetribe.com/docs/template/how-to-change-bundled-marketplace-texts/) and [how hosted marketplace texts work in the template](https://www.sharetribe.com/docs/template/hosted-marketplace-texts/).

You may also want to change the language of the user interface entirely. Read more about [changing the language used in the template](https://www.sharetribe.com/docs/template/how-to-change-template-language/).

In addition to marketplace texts, there are other forms of content in the client applications that operators may need to manage.

## Content pages

Your marketplace also has some content pages that can be modified through Sharetribe Console. The default content pages include:

- About
- Landing page
- Privacy Policy
- Terms of Service

These pages are rendered by the [PageBuilder component](https://www.sharetribe.com/docs/template/page-builder/) in the template. In addition to these default pages, you can create your own content pages through Sharetribe Console, and [fully manage their content](https://www.sharetribe.com/docs/concepts/content-management/) without code changes. On the template side, you can modify [how that content is displayed](https://www.sharetribe.com/docs/how-to/options-prop/).

## Static pages

It is possible to create fully static pages in the Sharetribe Web Template. You might want to do this if you want to create static content pages for performance reasons.

More information about adding static content to the application can be found in the [How to add static pages in the template](https://www.sharetribe.com/docs/template/how-to-add-static-pages/) guide.

## Labels and countries

There are a few other cases where we haven't added marketplace texts directly to the marketplace text files. 

Labels for filters can be found in [config/configListing.js](https://github.com/sharetribe/web-template/blob/main/src/config/configListing.js), and edited in Console.

[Country codes](https://github.com/sharetribe/web-template/blob/master/src/translations/countryCodes.js) are in a separate file as well. Stripe API requires country information as [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) codes. These are used when billing address is asked in `StripePaymentForm` on `CheckoutPage`.

```shell
└── src
    └── translations
        └── countryCodes.js
```

### Can't find what you’re looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```