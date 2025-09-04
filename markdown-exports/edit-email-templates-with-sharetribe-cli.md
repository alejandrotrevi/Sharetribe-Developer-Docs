---
title: "Edit email templates with Sharetribe CLI"
source: "provided"
extracted_date: "2025-09-04"
summary: "This tutorial shows you how to edit email templates using the Sharetribe Command-line interface."
---

- [Pull existing process](#pull-existing-process)
- [Templates directory](#templates-directory)
  - [Example](#example)
- [Email template syntax](#email-template-syntax)
- [Change a template](#change-a-template)
- [Preview your changes](#preview-your-changes)
- [Sending a preview email](#sending-a-preview-email)
- [Sample email context](#sample-email-context)
- [Push new version](#push-new-version)
- [Update alias](#update-alias)
- [Summary](#summary)

# Edit email templates with Sharetribe CLI

This tutorial shows you how to edit email templates using the Sharetribe Command-line interface.

**Required skills:** basic command line, text editing

Sharetribe CLI (Command-line interface) is a tool for changing your marketplace's advanced configurations such as transaction processes and email templates.

This guide expects that you have already installed Sharetribe CLI and are logged in with your API key. If not, it's recommended to first read the guide [Getting started with Sharetribe CLI](/docs/introduction/getting-started-with-sharetribe-cli/).

We also recommend that you go through the [Edit transaction process with Sharetribe CLI](/docs/how-to/edit-transaction-process-with-sharetribe-cli/) tutorial to understand process pulling, editing, pushing, and alias handling on a general level.

In this tutorial we make a change to an email template that is used in sending notifications to your marketplace users as part of your transaction process. These transaction email template changes are also a form of process change, and they create a new version of your process. For email notifications not part of your transaction process, see the [Built-in email notifications](https://console.sharetribe.com/advanced/email-notifications) page in the Build > Advanced > Email notifications section of Console.

> **Information**
> 
> For content-only changes, you can use the Console transaction email editor in Build > Content > Email texts.

## Pull existing process

To edit the transaction email templates, you need to pull an existing process with its templates. First, let's list all the processes of our `my-marketplace-dev` marketplace:

```bash
flex-cli process list -m my-marketplace-dev
```

The `process list` command prints out all the processes and their latest versions. You want to pick the correct process and version from this list. In this tutorial we will use the `default-booking` process, version 1. You probably have different transaction processes in your marketplace - so, you need to adjust this guide accordingly.

Let's pull that process version:

```bash
flex-cli process pull --process default-booking --version 1 --path process -m my-marketplace-dev
```

This will create a `process/` directory that has all the process files in it:

- `process.edn` file, which describes the transaction process
- `templates` directory, which contains all the transaction email templates for this process

## Templates directory

Let's see what we have in the `process/` directory:

![Process directory contents](/docs/static/3593f2b58e86fa4e2fcfb733151957c3/53fb6/process-dir.png)

If you look at the `:notifications` key in the `process.edn` file, you will see that the template directories and file names match the `:template` values in the notifications:

```clojure
:notifications
 [{:name :notification/booking-new-request,
   :on :transition/confirm-payment,
   :to :actor.role/provider,
   :template :booking-new-request}
  {:name :notification/booking-accepted-request,
   :on :transition/accept,
   :to :actor.role/customer,
   :template :booking-accepted-request}
  {:name :notification/booking-operator-accepted-request-to-customer,
   :on :transition/operator-accept,
   :to :actor.role/customer,
   :template :booking-accepted-request}
...
```

A template for a notification is a directory that is named after the `:template` value and contains two files:

- `TEMPLATE_NAME-subject.txt` - holds the mail Subject line template
- `TEMPLATE_NAME-html.html` - contains the template for the HTML version of the mail

Both parts are mandatory. All emails that are sent from the marketplace contain both the HTML and plain text variants and the recipient's mail client is free to choose which one to visualize and how. The text version is automatically generated from the HTML template.

### Example

For example, the `:notification/booking-new-request` notification:

```clojure
{:name :notification/booking-new-request,
   :on :transition/confirm-payment,
   :to :actor.role/provider,
   :template :booking-new-request}
```

has the following template:

```
booking-new-request
├── booking-new-request-html.html
└── booking-new-request-subject.txt
```

Note that the template name (e.g. `:booking-new-request`) doesn't have to match the notification name (e.g. `:notification/booking-new-request`) as you can use the same template in multiple notifications.

## Email template syntax

The templates use [Handlebars](http://handlebarsjs.com/) syntax.

Example HTML:

```handlebars
<!--
** First, set global variables for the whole file
-->
    {{set-translations (asset "content/email-texts.json")}}
    {{set-locale (asset "general/localization.json" "locale" "en_US")}}
    {{set-timezone transaction.listing.availability-plan.timezone}}
<html lang="en">
  <head>
    <meta http-equiv="Content-Type" content="text/html charset=UTF-8" />
  </head>
  <!--
  ** Define inline functions used in the file to format data
  -->
  {{~#*inline "format-money"~}}{{format-text "{amount,number,::.00} {currency}" amount=money.amount currency=money.currency}}{{~/inline~}}
  {{~#*inline "format-day"~}}{{#with transaction.listing.availability-plan}}{{format-text "{date,date,::EE}" date=date}}{{/with}}{{~/inline~}}
  {{~#*inline "format-day-before"~}}{{#with transaction.listing.availability-plan}}{{format-text "{date,date,::EE}" date=(date-transform date days=-1)}}{{/with}}{{~/inline~}}
  {{~#*inline "format-day-time"~}}{{#with transaction.listing.availability-plan}}{{format-text "{date,date,::EEhmma}" date=date}}{{/with}}{{~/inline~}}
  {{~#*inline "format-month-date"~}}{{#with transaction.listing.availability-plan}}{{format-text "{date,date,::MMMd}" date=date}}{{/with}}{{~/inline~}}
  {{~#*inline "format-month-date-day-before"~}}{{#with transaction.listing.availability-plan}}{{format-text "{date,date,::MMMd}" date=(date-transform date days=-1)}}{{/with}}{{~/inline~}}
  <!--
  ** Use the #with helper to expose the attributes of the transaction to the enclosed block
  -->
  {{#with transaction}}
  <table style="background-color:#FFF;margin:0 auto;padding:24px 12px 0;font-family:-apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen', 'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif" align="center" border="0" cellPadding="0" cellSpacing="0" role="presentation" width="100%">
    <tbody>
      <tr>
        <td>
          <table align="center" role="presentation" cellSpacing="0" cellPadding="0" border="0" width="100%" style="max-width:600px;margin:0 auto">
            <tr style="width:100%">
              <td>
                <!--
                ** The email template uses the 't' helper for rendering content:
                ** - the first parameter is the message key
                ** -