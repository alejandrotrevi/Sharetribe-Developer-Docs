---
title: "View events with Sharetribe CLI"
source: "https://www.sharetribe.com/docs/how-to/view-events-with-sharetribe-cli/"
extracted_date: "2025-01-08"
summary: "This guide shows you how to query and view events data using Sharetribe CLI. Events tell you the change history of marketplace data resources and allow observing noteworthy events."
---

- [Querying events](#querying-events)
- [Output modes and examining events in detail](#output-modes-and-examining-events-in-detail)
- [Live tailing events](#live-tailing-events)
- [Summary](#summary)

# View events with Sharetribe CLI

This guide shows you how to query and view events data using Sharetribe CLI. Events tell you the change history of marketplace data resources and allow observing noteworthy events.

**Required skills:** basic command line, text editing

Sharetribe CLI (Command-line interface) is a tool for examining and changing your marketplace's advanced configurations such as transaction processes and email templates.

This guide expects that you have already installed Sharetribe CLI and are logged in with your API key. If not, it's recommended to first read the tutorial [Getting started with Sharetribe CLI](/docs/introduction/getting-started-with-sharetribe-cli/).

In this guide, we will learn how the events of a marketplace can be queried using Sharetribe CLI, how we can inspect events in more detail as well as combine Sharetribe CLI with other tools to further process the event data.

In Sharetribe, events represent changes in marketplace data resources such as listings, users and transactions. An event captures a single change in marketplace data, e.g. a user being created or a listing being updated. Events can be further analyzed to interpret them as logical actions such as a listing being published, a message being sent or a user having changed their email address by looking into what were the changed data fields. To learn more about events, have a look at the [Events](/docs/references/events/) reference.

## Querying events

The command for querying events using Sharetribe CLI is `events`.

```bash
$ flex-cli help events
Get a list of events.

USAGE
  $ flex-cli events

OPTIONS
  --after-seqid=SEQUENCE_ID               Show events with sequence ID larger than (after) the specified.
  --after-ts=TIMESTAMP                    Show events created after the given timestamp, e.g. '--after-ts 2020-10-10' or '--after-ts 2020-10-10T10:00.000Z'
  --before-seqid=SEQUENCE_ID              Show events with sequence ID smaller than (before) the specified.
  --before-ts=TIMESTAMP                   Show events created before the given timestamp, e.g. '--before-ts 2020-11-15' or '--before-ts 2020-11-15T12:00.000Z'
  --filter=EVENT_TYPES                    Show only events of given types, e.g. '--filter listing/updated,user'.
  --json                                  Print full event data as one JSON string.
  --json-pretty                           Print full event data as indented multi-line JSON string.
  --related-resource=RELATED_RESOURCE_ID  Show events that are related to a specific resource ID.
  --resource=RESOURCE_ID                  Show events for a specific resource ID only.
  --seqid=SEQUENCE_ID                     Get only the event with the given sequence id.
  -l, --limit=NUMBER                      Show given number of events (default and max is 100). Can be combined with other parameters.
  -m, --marketplace=MARKETPLACE_ID        marketplace identifier
```

Events command supports various ways to query events. Querying without any of the additional parameters lists the 100 latest events for your marketplace:

```text
$ flex-cli events -m my-marketplace-dev

Seq ID   Resource ID                           Event type                     Created at local time   Source           Actor
3391589  5fca1e5b-2004-4479-a68c-dfc8a03083b8  availabilityException/created  2020-12-04 1:32:43 PM   marketplace-api  jane@example.com
3391590  5fca1e5c-eda8-4f54-ac30-ee7fe1010d11  availabilityException/created  2020-12-04 1:32:44 PM   marketplace-api  jane@example.com
3462923  5dfb4a42-8937-47b5-b482-44679828939c  user/updated                   2020-12-07 3:17:30 PM   console          joe@example.com
3471843  5fce8536-61f5-4c85-8160-61b1799d256f  user/updated                   2020-12-07 9:45:38 PM   marketplace-api  joe@example.com
3471856  5fce86c7-e435-4047-ab3b-dc4fee02d51d  listing/created                2020-12-07 9:47:19 PM   marketplace-api  joe@example.com
3471857  5fce86c7-e435-4047-ab3b-dc4fee02d51d  listing/updated                2020-12-07 9:47:28 PM   marketplace-api  joe@example.com
3471858  5fce86c7-e435-4047-ab3b-dc4fee02d51d  listing/updated                2020-12-07 9:47:38 PM   marketplace-api  joe@example.com
3471859  5fce86c7-e435-4047-ab3b-dc4fee02d51d  listing/updated                2020-12-07 9:47:53 PM   marketplace-api  joe@example.com
3471860  5fce86c7-e435-4047-ab3b-dc4fee02d51d  listing/updated                2020-12-07 9:47:56 PM   marketplace-api  joe@example.com
3471863  5fce8728-0f15-46e4-8ee2-c4955e0cc079  availabilityException/created  2020-12-07 9:48:56 PM   marketplace-api  joe@example.com
3471864  5fce872f-4081-4ca7-a40d-6eb2b003dcc2  availabilityException/created  2020-12-07 9:49:03 PM   marketplace-api  joe@example.com
3471865  5fce8730-c750-4427-bc58-09f685cc0b03  availabilityException/created  2020-12-07 9:49:04 PM   marketplace-api  joe@example.com
3471866  5fce86c7-e435-4047-ab3b-dc4fee02d51d  listing/updated                2020-12-07 9:49:11 PM   marketplace-api  joe@example.com
3471873  5fce86c7-e435-4047-ab3b-dc4fee02d51d  listing/updated                2020-12-07 9:50:35 PM   marketplace-api  joe@example.com
3471875  5fce8536-61f5-4c85-8160-61b1799d256f  user/updated                   2020-12-07 9:50:52 PM   marketplace-api  joe@example.com
3471890  5fce86c7-e435-4047-ab3b-dc4fee02d51d  listing/updated                2020-12-07 9:53:15 PM   marketplace-api  joe@example.com
...
```

The default table output mode lists a few key [event attributes](/docs/references/events/#event-attributes). It also shows an email address of the actor, i.e. the person who took the action that caused the event.

By adding the `--limit` (short version `-l`) parameter we can limit the output to given number of events:

```bash
$ flex-cli events -l 2 -m my-marketplace-dev

Seq ID   Resource ID                           Event type                     Created at local time   Source           Actor
3391589  5fca1e5b-2004-4479-a68c-dfc8a03083b8  availabilityException/created  2020