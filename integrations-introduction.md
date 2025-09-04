```markdown
---
title: "Introduction to integrations in Sharetribe"
source: "provided"
extracted_date: "2024-02-07"
summary: "Sharetribe allows integrations with a vast range of third-party tools and solutions."
---

- [Table of Contents](#table-of-contents)
- [Sharetribe integration tools](#sharetribe-integration-tools)
- [Client application](#client-application)
- [Integration API](#integration-api)
- [Events](#events)
- [Zapier](#zapier)
- [Levels of integration](#levels-of-integration)
- [Examples of specific integrations](#examples-of-specific-integrations)
  - [Hotjar analytics](#hotjar-analytics)
  - [Mailchimp email list](#mailchimp-email-list)
  - [Twilio SMS](#twilio-sms)
  - [Sendbird user-to-user chat app](#sendbird-user-to-user-chat-app)
  - [Voucherify discount coupons](#voucherify-discount-coupons)
  - [Google Calendar synchronised with Sharetribe availability calendar](#google-calendar-synchronised-with-sharetribe-availability-calendar)
  - [Payment gateway integration](#payment-gateway-integration)

# Introduction to integrations in Sharetribe
Sharetribe allows integrations with a vast range of third-party tools and solutions. These tools can be integrated with Sharetribe by using Zapier, the Sharetribe Integration API, or the client application.

## Sharetribe integration tools
There are several ways to approach integrating third-party tools with your Sharetribe marketplace. Your integration options within Sharetribe depend on the requirements of the third-party tool you are integrating and on the complexity of the integration you are looking to create.

### Client application
The simplest integration tool is your client application. Since you have full control over your marketplace client app, you can add analytics and tracking tools such as [Hotjar](#hotjar-analytics) or [Google Analytics](https://www.google.com/analytics/) directly into your client app code.

The Sharetribe Web Template includes a Node.js/Express server by default. Some third-party integrations, such as [Voucherify](#voucherify-discount-coupons) or other promotion integrations, may require a secure context. For these types of integrations, you can use the template server to create endpoints or other logic in a secure way.

### Integration API
If you want to create an integration where the third-party solution listens to – or takes action on – your Sharetribe marketplace, you will likely need to use the [Integration API](https://www.sharetribe.com/docs/introduction/getting-started-with-integration-api/). The Integration API gives operators powerful access to all marketplace data and activity.

Due to its powerful nature, the Integration API should **only ever be used from a secure context**, such as your client application server or a separate backend application.

### Events
In addition to direct endpoints to marketplace resources, the Integration API also exposes events. You can review [the full list of supported event types](https://www.sharetribe.com/docs/references/events/#supported-event-types).

By listening to events, you can build flows where third-party solutions take action either in Sharetribe or within the solutions themselves. You can read more about [reacting to events in Sharetribe](https://www.sharetribe.com/docs/how-to/reacting-to-events/).

### Zapier
Sharetribe has also built a [Zapier integration](https://www.sharetribe.com/help/en/collections/8975364) to facilitate integrating third-party tools with Sharetribe. The Zapier integration works by harnessing a subset of Sharetribe events and allows operators to connect any of the other [5000+ tools and solutions](https://zapier.com/apps/) in the Zapier ecosystem.

## Levels of integration
The complexity of a Sharetribe integration with a third-party tool can range from very simple to highly complex. One aspect determining the complexity is how intertwined the two tools need to be. 

**Simple integrations:**
- Calling third-party APIs and SDKs from your client application.
- Adding a third-party script to your client application.
- Adding a custom endpoint in your client app server that calls a third-party API.
- Listening to Sharetribe events using Zapier.

**More complex integrations:**
- Listening to Sharetribe events using your own polling tool.
- Synchronizing Sharetribe and a parallel system to update each other when any changes happen.

## Examples of specific integrations

### Hotjar analytics
[Hotjar](https://www.hotjar.com/) is an analytics tool for websites that allows you to collect user activity heatmaps, feedback, and user behavior recordings.
- Add a tracking script to the client application.
- Add custom CSP directives.

### Mailchimp email list
[Mailchimp](https://mailchimp.com/) is an email marketing and automations tool used to communicate directly with your marketplace users.
- Use the sample Zapier template for adding new users to your Mailchimp mailing lists.

### Twilio SMS
[Twilio](https://www.twilio.com/) offers multiple channels of communicating with your marketplace users.
- Follow the [Sharetribe tutorial](https://www.sharetribe.com/help/en/articles/8788994) to send providers a SMS message whenever their listing gets booked.

### Sendbird user-to-user chat app
[Sendbird](https://sendbird.com/) allows for more complex messaging flows between users.
- Create an account in Sendbird.
- Add the Sendbird UI kit and SDK.

### Voucherify discount coupons
[Voucherify](https://www.voucherify.io/) is a promotions tool that allows you to create, validate and redeem discount coupons.
- Create an account in Voucherify and create your own codes or use the sample codes.

### Google Calendar synchronised with Sharetribe availability calendar
If you run a booking marketplace, you may want to allow providers to synchronize their own [Google Calendar](https://developers.google.com/calendar) with Sharetribe.
- Google provides [Calendar API](https://developers.google.com/calendar/api) to modify calendar events.

### Payment gateway integration
Integrating a third-party payment gateway, such as [Paypal Commerce Platform](https://www.paypal.com/us/business/platforms-and-marketplaces) or [Stripe Billing](https://stripe.com/en-gb/billing), is a complex and extensive integration.
- Contact the support team of the payment gateway tool you intend to use.
- Familiarize yourself with the article on [integrating a third-party payment gateway to Sharetribe](https://www.sharetribe.com/docs/how-to/how-to-integrate-3rd-party-payment-gateway/).
```