```markdown
---
title: "Log errors with Sentry"
source: "provided"
extracted_date: "2023-10-24"
summary: "This guide documents how to use Sentry to log errors in the Sharetribe Web Template."
---

- [Table of Contents](#table-of-contents)
  - [Error logging](#error-logging)
  - [Configure the Sentry DSN variable](#configure-the-sentry-dsn-variable)
  - [Configure logging with basic access authentication](#configure-logging-with-basic-access-authentication)

# Log errors with Sentry

This guide documents how to use Sentry to log errors in the Sharetribe Web Template.

## Table of Contents
- [Error logging](#error-logging)
- [Configure the Sentry DSN variable](#configure-the-sentry-dsn-variable)
- [Configure logging with basic access authentication](#configure-logging-with-basic-access-authentication)

## Error logging

The Sharetribe Web Template supports error logging with [Sentry](https://sentry.io/) out of the box, provided that the required environment variables are set in place. Other logging solutions can also be used but the Sentry client comes already strapped into the application.

### Configure the Sentry DSN variable

To enable the Sentry error logging a *Data Source Name* (DSN) has to be provided as an environment variable. The browser and Node environments both use the `REACT_APP_SENTRY_DSN` variable.

You can acquire the DNS key from your Sentry project settings. To test it in your local environment, you can either assign it to the `REACT_APP_SENTRY_DSN` environment variable through the `.env` file or when running the `yarn run dev-server` command:

```bash
REACT_APP_SENTRY_DSN='<sentry-dsn>' yarn run dev-server
```

The template will log all errors to the console if you do not configure the Sentry DNS key. You can find all code associated with error logging and Sentry in [src/util/log.js](https://github.com/sharetribe/web-template/blob/master/src/util/log.js).

### Configure logging with basic access authentication

By default, Sentry fetches the source maps for minified JavaScript files. However, that might not work if authentication, such as [basic access authentication](/docs/tutorial/deploy-to-render/#enable-http-basic-access-authentication), is enabled. If you want to enable logging to Sentry while having basic access authentication enabled, you can pass Sentry a security token. Sentry then adds this token to the source maps request header, which allows the server to let those requests access the source maps.

To enable logging while having basic access authentication enabled, you can configure the security token in the Sentry dashboard by navigating to:

**Project > Settings > General > Client Security**

Set the following values:

- Security token - Basic `<your username:password in Base 64>`
- Security token header - Authorization

---

### Can't find what you're looking for?

Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.

---

© [Sharetribe](https://www.sharetribe.com) 2025.
```