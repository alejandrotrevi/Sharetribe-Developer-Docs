```markdown
---
title: "How to change the default favicons"
source: "provided"
extracted_date: "2023-10-24"
summary: "This guide describes how to use your own custom favicon and application icons in the Sharetribe Web Template."
---

- [Table of Contents](#table-of-contents)

# How to change the default favicons

This guide describes how to use your own custom favicon and application icons in the Sharetribe Web Template.

## Table of Contents
- [Generate new favicons](#generate-new-favicons)

## Generate new favicons

By default, the Sharetribe Web Template uses favicons and application icons generated through Console. You can also use [RealFaviconGenerator](https://realfavicongenerator.net/) to generate your own set of favicons, if necessary.

You can upload your original icon to the tool, customize the colors and themes, and download a generated set of icons and an HTML snippet to point to those images.

1. Open [RealFaviconGenerator](https://realfavicongenerator.net/).
2. Upload your original icon image.
3. Configure platform-specific icons.
   > **Note:** Remember to set the "Theme color" in the Android Chrome section.
4. Configure the paths to use `/static/icons/` as the root path of the icons.
5. Generate the icons.
6. Unzip the `favicons.zip` archive and replace the default icons and files in [public/static/icons/](https://github.com/sharetribe/web-template/tree/main/public/static/icons) with the new icons.
7. Replace the default HTML snippet in [public/index.html](https://github.com/sharetribe/web-template/blob/main/public/index.html) with the snippet from the generator.

### Information
Remove the manifest link from the snippet as we have a default manifest with extra data compared to the generated one. You can edit the default file as you wish.

**Example HTML snippet:**

```html
<!-- Start Favicons from https://realfavicongenerator.net/ -->
<link rel="apple-touch-icon" sizes="180x180" href="/static/icons/apple-touch-icon.png">
<link rel="icon" type="image/png" sizes="32x32" href="/static/icons/favicon-32x32.png">
<link rel="icon" type="image/png" sizes="16x16" href="/static/icons/favicon-16x16.png">
<link rel="mask-icon" href="/static/icons/safari-pinned-tab.svg" color="#c0392b">
<link rel="shortcut icon" href="/static/icons/favicon.ico">
<meta name="msapplication-config" content="/static/icons/browserconfig.xml">
<meta name="theme-color" content="#c0392b">
<!-- End Favicons -->
```

## Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```