```markdown
---
title: "Messages"
source: "provided"
extracted_date: "2023-10-24"
summary: "Sharetribe allows your users to communicate with each other using messages."
---

- [Overview](#overview)
- [Sending messages](#sending-messages)
- [Querying messages](#querying-messages)
- [Email notifications](#email-notifications)
- [Zapier, events and messages](#zapier-events-and-messages)
- [How to retrieve messages in Zapier](#how-to-retrieve-messages-in-zapier)
- [Events and messages](#events-and-messages)
- [Message notifications in Sharetribe Web Template](#message-notifications-in-sharetribe-web-template)

# Overview
Sharetribe allows your users to communicate with each other using messages. This article introduces the concept of messages and how to access messages through Zapier.

## What are messages?
Messages let your users communicate with other users in your marketplace. They can be exchanged freely between a customer and a provider once they have engaged in a transaction. Messages always need to be associated with a transaction and cannot be sent outside of one.

The default booking and purchase [transaction processes](https://github.com/sharetribe/example-processes/blob/master/default-booking/process.edn) include an inquiry transition, which initiates a transaction without running any [actions](/docs/references/transaction-process-actions/#actions), allowing the provider and customer to send messages to each other. In addition, the [default-inquiry process](https://github.com/sharetribe/example-processes/blob/master/default-inquiry/process.edn) initiates a simple transaction that is only intended for messaging.

Note that messages do not alter the transaction or transition it to a different state.

## Sending messages
You can send messages using the [send message endpoint](https://www.sharetribe.com/api-reference/marketplace.html#send-message), which requires an authenticated user’s access token to call. The [Integration API](/docs/introduction/getting-started-with-integration-api/) does not offer an endpoint to send messages, and therefore, only authenticated users can send messages through the Marketplace API.

## Querying messages
You can query messages through the query messages endpoint, which returns all messages in a given transaction. Messages can also be included as a relationship when [querying transactions](https://www.sharetribe.com/api-reference/marketplace.html#query-transactions).

## Email notifications
New messages trigger a [built-in email notification](https://www.sharetribe.com/docs/concepts/email-notifications/) sent to the receiving party of the message. You can edit built-in email notification content through [Console > Build > Content > Email texts](https://console.sharetribe.com/a/content/email-texts), and you can modify the structure and code of the notifications through [Console](https://console.sharetribe.com/email-templates/new-message).

## Zapier, events and messages
Using [Zapier](https://www.sharetribe.com/help/en/collections/8975364) you can connect your marketplace with other web applications and create automated workflows.

Even though you can’t listen for new messages through Zapier, messages can easily be retrieved as a transaction relationship. For more complex customizations, you can use events to listen to new or deleted messages.

### How to retrieve messages in Zapier
You can use Zapier to access messages using the transaction ID that is associated with them. By default, when you listen to transaction events, the message relationship is not included. To include the message relationship, add the action "Show Transaction" to the trigger "Transaction events" and select messages from the dropdown menu.

![](https://www.sharetribe.com/docs/06d90691ec97fddc3c3eb7bcba659ac6/zapier.mp4#t=0.1)

From the dropdown menu, you can select messages, and you are then able to use the message content in your Zap.

## Events and messages
Listening to [events](https://docs/sharetribe.com/how-to/reacting-to-events/) through the [Integration API](/docs/introduction/getting-started-with-integration-api/) is the most versatile way to react to what is happening in your marketplace. As sending new messages does not affect transaction state or transitions, you can’t use Zapier to detect new messages as it can only react to transactions, listing and user events. Events allow you to listen to [created messages](https://docs/sharetribe.com/references/events/#supported-event-types) and react directly to them. See how to [react to events](https://docs/sharetribe.com/how-to/reacting-to-events/) and the [Integration API example script repository](https://github.com/sharetribe/integration-api-examples) if you’re unsure where to start building your integration.

## Message notifications in Sharetribe Web Template
By default, Sharetribe Web Template renders a notification symbol when the provider has transactions that require action, i.e. transactions that require acceptance of a booking request.

![Notification symbol](https://www.sharetribe.com/docs/static/94bf0995e96b11f5153ea9fccc8a9427/81b43/notification.png)

This is how the default logic works:
1. A [query is made](https://github.com/sharetribe/web-template/blob/main/src/ducks/user.duck.js#L306) that retrieves all sales transactions (i.e. transactions where the current user is the provider) transactions that are in a state that requires provider attention.
2. The amount of sales transactions determines the [notification count](https://github.com/sharetribe/web-template/blob/main/src/ducks/user.duck.js#L105) shown in the badge.

The variable [currentUserNotificationCount](https://github.com/sharetribe/web-template/blob/main/src/ducks/user.duck.js#L64) stores the number of active notifications.

You can extend the messaging logic in many ways. For example, a common customization is to display a notification every time a user receives a new message. To achieve this, you could change the logic behind [currentUserNotificationCount](https://github.com/sharetribe/web-template/blob/main/src/ducks/user.duck.js#L64) to display a number stored in extended data. The data attribute would represent the number of unread messages, and could be updated every time a new message is detected using [events](/docs/how-to/reacting-to-events/).
```