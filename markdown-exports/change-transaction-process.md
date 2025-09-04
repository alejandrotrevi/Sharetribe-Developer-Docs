```markdown
---
title: "Changing Your Transaction Process"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2023-10-24"
summary: "How to create and communicate a new transaction processes or make changes to an existing one."
---

- [Table of Contents](#table-of-contents)
- [What to think about in designing your own transaction process](#what-to-think-about-in-designing-your-own-transaction-process)
- [Pricing](#pricing)
- [Payments](#payments)
- [Bookings or stock](#bookings-or-stock)
- [Shared information](#shared-information)
- [Email notifications](#email-notifications)
- [Reviews](#reviews)
- [Draw it out](#draw-it-out)
- [Modify an existing process, if you can](#modify-an-existing-process-if-you-can)
- [Remember your marketplace website or app](#remember-your-marketplace-website-or-app)
- [Share your transaction process with your developer](#share-your-transaction-process-with-your-developer)
- [We’re here to help](#were-here-to-help)
- [Start designing](#start-designing)

# Changing Your Transaction Process
How to create and communicate a new transaction processes or make changes to an existing one.

## What to think about in designing your own transaction process
When you’re designing your transaction process, you should be thinking of how you want your users to interact between each other and with the platform. Most marketplace transactions contain at least some of the following elements.

### Pricing
Consider how the price of a transaction is calculated. Likely, the type of marketplace you’re building will be a strong determinant of how prices are determined. A marketplace facilitating daily rentals, for instance, probably calculates prices based on the length of a booking.

### Payments
Payments are a typical component of online marketplaces. Sharetribe already has an integration with Stripe Connect using Custom Accounts, a payment gateway specifically designed for marketplaces allowing credit and debit card payments, commissions, and delayed payouts.

### Bookings or stock
Booking is a process specific to reserving a time slot from a provider’s availability calendar. If your marketplace includes bookings, outline how they will work.

### Shared information
Sometimes, additional information needs to be shared between a customer and provider during a transaction. Messaging is a common feature in marketplaces for this reason, and comes included in Sharetribe.

### Email notifications
You can send email notifications to your customers and providers throughout a transaction. Specifically, an email can be sent whenever a transition occurs. 

### Reviews
Reviews are an important part of most marketplaces. You should think about the following:
- Can both parties review each other, or is it only customers reviewing providers?
- What is the time period during which a review can be made?

To further think about the user experience on your marketplace in addition to transactions, [consult our guide](https://www.sharetribe.com/docs/design-toolkit/what-are-user-journeys/) on defining the discovery and listing creating process in your marketplace.

## Draw it out
It’s time to put the pieces of your marketplace transaction into a coherent journey. Drawing a graph is the best way to illustrate this journey.

## Modify an existing process, if you can
Modifying an existing process is easier than building one from scratch. Think of a marketplace similar to yours and write down the steps you completed last time you used the platform.

## Remember your marketplace website or app
The transaction process plays out in your web or mobile marketplace application. Therefore, the steps you outline must be possible for your users to execute in your user interface.

## Share your transaction process with your developer
If you’re working with a developer to build your marketplace, share the outlines of how a transaction should happen on your marketplace with your developer early.

## We’re here to help
Building with a technical solution may be new to you. If you have any questions or concerns, don’t hesitate to reach out to our team at [hello@sharetribe.com](mailto:hello@sharetribe.com). 

## Start designing
Whimsical is an intuitive and easy-to-use tool to communicate visually. You can create mindmaps, wireframes, and most importantly, flowcharts to communicate your transaction process clearly to your developers.

Follow these steps:
1. Go [to this address](https://whimsical.com/flex-default-transaction-process-GDRUyuF7M5nMSte6rfwNuc).
2. Sign in to your Whimsical account or create an account if you don't have one.
3. On the top right, click on "COPY TO" and select your private folder.
4. That’s it! You can now start designing your own transaction process.

![How to copy the project](https://example.com/copy-whimsical-project.png)
```