```markdown
---
title: "Customization checklist"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2023-10-24"
summary: "This article provides a reference list of common customisations to aid you in development."
---

- [Table of Contents](#table-of-contents)
  - [Visual appearance](#visual-appearance)
  - [Content and marketplace texts](#content-and-marketplace-texts)
  - [Configuration](#configuration)
  - [Other optional changes](#other-optional-changes)

# Customization checklist

This article provides a reference list of common customisations to aid you in development.

## Visual appearance

You can edit the default styles of the template via the branding tab in Console. For more advanced changes you can:

- [Learn to style the template using CSS](https://www.sharetribe.com/docs/tutorial/first-edit/)
- Learn more about the [CSS architecture in the Sharetribe Web Template](https://www.sharetribe.com/docs/template/how-to-customize-template-styles/)

## Content and marketplace texts

In addition to customizing the default styles of your marketplace, it is also important to change the content and [marketplace texts](https://www.sharetribe.com/docs/concepts/marketplace-texts/) to reflect your brand and messaging. There are several places where you can edit the content and copy texts in your marketplace:

- Learn about [marketplace texts](https://www.sharetribe.com/docs/concepts/marketplace-texts/)
- Update your [terms of service and privacy policy page](https://www.sharetribe.com/help/en/articles/8410839-free-templates-for-your-terms-of-service-and-privacy-policy).
- Configure the footer of your marketplace in Sharetribe Console.
- Update the [email templates](https://www.sharetribe.com/docs/concepts/email-notifications/): you will want to update the templates to reflect your brand and messaging.

## Configuration

The configuration file of your marketplace template is a valuable resource that allows you to easily adjust a wide range of options in your marketplace. Configuration files can be found in the [src/config directory](https://github.com/sharetribe/web-template/tree/main/src/config). Before you begin modifying the configuration files, it is important to ensure that you have properly configured any environment variables that your marketplace uses. These variables are typically used to store sensitive information such as API keys, and must be set up correctly in order for your marketplace to function properly.

- Configure your [environment variables](https://www.sharetribe.com/docs/template/template-env/). You can also run `yarn run config` in the root directory of the template, which will walk you through the setup process.
- Go through the configuration files of your marketplace following this guide.

## Other optional changes

In addition to customizing the default styles, content and copy texts, and configuration options of your marketplace, there are a number of other changes you may want to make. Some of these options include:

- [Customize pricing](https://www.sharetribe.com/docs/tutorial/customize-pricing-tutorial/)
- Update your transaction process
- Create new static pages
- Update the routing in your marketplace
- Update transaction email templates. For more information, see [Edit email templates with Sharetribe CLI](https://www.sharetribe.com/docs/how-to/edit-email-templates-with-sharetribe-cli/) tutorial and [Email templates](https://www.sharetribe.com/docs/references/email-templates/) reference article.
- Add more content pages: [How the templates render content pages using the PageBuilder](https://www.sharetribe.com/docs/template/page-builder/)
- Update other existing pages

---

### Can't find what you're looking for?

Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.

```