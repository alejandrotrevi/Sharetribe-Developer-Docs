---
title: "Reacting to events"
source: "provided"
extracted_date: "2025-01-01"
summary: "This guide demonstrates how to build an integration that reacts to events that happen in your Sharetribe marketplace, using the Sharetribe Integration API."
---

- [Querying events](#querying-events)
- [Using event data to detect change](#using-event-data-to-detect-change)
- [Polling events continuously using sequence IDs](#polling-events-continuously-using-sequence-ids)
  - [Persisting the last seen sequence ID](#persisting-the-last-seen-sequence-id)
  - [Recommended polling interval](#recommended-polling-interval)
  - [Poll loop example using local file to store state](#poll-loop-example-using-local-file-to-store-state)
- [At-least-once and at-most-once event processing](#at-least-once-and-at-most-once-event-processing)
- [Additional resources](#additional-resources)

# Reacting to events

This guide demonstrates how to build an integration that reacts to events that happen in your Sharetribe marketplace, using the Sharetribe Integration API.

One of the main purposes of [events](https://www.sharetribe.com/docs/references/events/) in Sharetribe is to allow building integrations that programatically react to changes and actions in a marketplace. In this guide, we will show how to use the Sharetribe Integration API to continuously query events efficiently. We will also demonstrate how to interpret the event data to detect actions, using publishing a listing as an example.

This guide assumes that you have already set up a Sharetribe Integration API application and have a Node.js app with the [Integration API SDK](https://www.sharetribe.com/docs/concepts/js-sdk/) ready. If you have not yet used the Sharetribe Integration API, follow the [Getting started with the Integration API](https://www.sharetribe.com/docs/introduction/getting-started-with-integration-api/) guide first and you will be ready to proceed with this guide.

In this guide we will cover the following main topics:

- querying events via the Integration API, using filters to receive only relevant events
- using event data to determine the change that happened
- understanding event sequence IDs and using them to correctly query for new events
- understanding at-least-once and at-most-once types of event processing

## Querying events

The Integration API [/events/query](https://www.sharetribe.com/api-reference/integration.html#query-events) endpoint will be used at the core of this guide. In the most basic form, a query for all available events using the SDK looks like this:

```javascript
integrationSdk.events.query();
```

In this guide, we are interested in detecting when a new listing is published and therefore do not need to process all events that happen in the marketplace. Instead, we can filter out only the relevant listing-related events. Recall that the [listing `state`](https://www.sharetribe.com/api-reference/integration.html#listing-states) attribute indicates whether a listing is `draft` or `published` (among other possible states) and that a new listing may become published in one of three ways:

- a listing can be [created as published](https://www.sharetribe.com/api-reference/marketplace.html#create-listing)
- a listing can be created as [draft](https://www.sharetribe.com/api-reference/marketplace.html#create-draft-listing) and be [published](https://www.sharetribe.com/api-reference/marketplace.html#publish-draft-listing) separately
- in case your marketplace has the [listing approval feature](https://www.sharetribe.com/docs/concepts/requiring-approval/) on, a listing that is pending approval becomes published when it is approved by an operator

Therefore, in order to cover all cases, we need to process both `listing/created` and `listing/updated` events. A query for only these types of events looks like this:

```javascript
integrationSdk.events.query({
  eventTypes: 'listing/created,listing/updated',
});
```

This would still give us all events with the given types that Sharetribe keeps in history. In practice, often we are interested in *new* events only. In the absense of another point of reference, it is possible to query events starting from a given timestamp:

```javascript
const now = new Date();
const fiveMinutesAgo = new Date(now - 300000);
integrationSdk.events.query({
  createdAtStart: fiveMinutesAgo,
  eventTypes: 'listing/created,listing/updated',
});
```

Later in the guide, we will see how to use the data of events that the application has already processed to query strictly for subsequent events.

## Using event data to detect change

Even with the event type filtering in place, not all `listing/created` or `listing/updated` events represent the logical change we may be interested in (such as a listing being published for the first time). We need to use the event [data](https://www.sharetribe.com/docs/references/events/#event-data) and in particular the [resource and previousValues](https://www.sharetribe.com/docs/references/events/#resource-data-and-previous-values) to detect which events correspond to the change we want to react to.

A new listing is first published when one of these happen:

- a `listing/created` event shows that the current listing state is `published`
- a `listing/updated` event shows that the current listing state is `published` and the previous state was `draft`
- a `listing/updated` event shows that the current listing state is `published` and the previous state was `pendingApproval`

In code, analyzing the event data can be done like this:

```javascript
const now = new Date();
const fiveMinutesAgo = new Date(now - 300000);

const handleListingPublished = event => {
  const { resourceId, resource: listing } = event.attributes;
  const listingId = resourceId.uuid;
  const authorId = listing.relationships.author.data.id.uuid;

  // Do something about the new published listing, such as send notification,
  // synchronize data to external system, etc.
  console.log(
    `A new listing has been published: listingId ${listingId}, author ID: ${authorId}`
  );
};

const analyzeEvent = event => {
  const {
    resource: listing,
    previousValues,
    eventType,
  } = event.attributes;
  const listingState = listing.attributes.state;
  const { state: previousState } = previousValues.attributes || {};

  const isPublished = listingState === 'published';
  const isPendingApproval = listingState === 'pendingApproval';
  const wasDraft = previousState === 'draft';
  const wasPendingApproval = previousState === 'pendingApproval';

  switch (eventType) {
    case 'listing/created':
      if (isPublished) {
        handleListingPublished(event);
      }
      break;
    case 'listing/updated':
      if (isPublished && (wasPendingApproval || wasDraft)) {
        handleListingPublished(event);
      }
      break;
  }
};

integrationSdk.events
  .query({
    createdAtStart: fiveMinutesAgo,
    eventTypes: 'listing/created,listing/updated',
  })
  .then(res => {
    const events = res.data.data;

    events.forEach(analyzeEvent);
  });
```

Naturally, instead of simply logging the event, the application can be doing something else, such as sending an email notification to a marketplace operator, synchronising data with an external system and so on.

## Polling events continuously using sequence IDs

In the guide so far we saw how to process events from a single query. In practice, an application that reacts to events needs to poll for new events continuously. In this section we will show how this is best achieved.

In Sharetribe, each event has a unique `sequenceId` and the [sequence IDs are strictly increasing](https://www.sharetribe.com/docs/references/events/#event-sequence-ids). Moreover, the `/events/query` Integration API endpoint always returns events sorted by their sequence IDs in ascending order. This means that once a batch of events is processed, the sequence ID of the last event can be used to query for strictly newer events, using the `startAfterSequenceId` [query