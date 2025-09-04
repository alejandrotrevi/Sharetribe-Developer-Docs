---
title: "Introducing Sharetribe Web Template"
source: "provided"
extracted_date: "2024-02-07"
summary: "Learn about the out of the box functionalities available in the Sharetribe Web Template and how to take them into use."
---

- [Customization starting point](#customization-starting-point)
- [Listings](#listings)
  - [Listing thumbnails and images](#listing-thumbnails-and-images)
  - [Availability](#availability)
  - [Inventory management](#inventory-management)
- [Search](#search)
  - [Keyword or location search](#keyword-or-location-search)
  - [Browsing and filtering](#browsing-and-filtering)
- [Transactions](#transactions)
  - [Booking default process](#booking-default-process)
  - [Product default process](#product-default-process)
  - [Payments](#payments)
  - [Messaging](#messaging)
  - [Reviews](#reviews)
- [Account management](#account-management)
  - [Signup and login](#signup-and-login)
  - [Seller onboarding](#seller-onboarding)
  - [Email notifications](#email-notifications)
- [Design](#design)
- [Customizing your own marketplace](#customizing-your-own-marketplace)

# Introducing Sharetribe Web Template

The Sharetribe Web Template is a customizable marketplace web application built on top of the Sharetribe APIs. It is a great starting point for building any type of marketplace, whether it is about rentals, services, products, or something else. You can configure and launch your marketplace without coding, then host the template code to add custom features.

## Customization starting point

You can configure a hosted version of the Sharetribe Web Template in your Console's Test environment. When you first open your Test marketplace, you start with a generic example marketplace.

You can change the default settings in Console. Change core user experiences like search, listing creation, and transactions in Console's Build section under the "Content" tab. For a full list of the no-code capabilities and how to use them to customise your marketplace, please consult our [guide on building your marketplace](https://www.sharetribe.com/help/en/articles/8501769-how-to-build-a-marketplace-with-sharetribe).

After configuring your marketplace, you may want to install the Template codebase on your own servers and modify the Template codebase with your own custom features in addition to the configurations you made without coding. Please start with [this guide](https://www.sharetribe.com/docs/introduction/getting-started-with-web-template/) on how to install the Template locally.

The rest of this article describes some of the out of the box functionality available in the Sharetribe Web Template.

## Listings

The heart of the Sharetribe Web Template marketplace are listings. Listings represent what is offered on a Sharetribe marketplace, whether it is a product, a job, a service, rental or something else.

A few configurations make it easy to set what your listings are about. By default, registered users can create any number of listings. Listings are automatically published. Published listings are visible to registered and non-registered users.

You can learn more about how listings work in Sharetribe [here](/docs/concepts/listings-overview/).

### Listing thumbnails and images

The template code has an easy configuration to change the aspect ratio of the thumbnails. The Sharetribe Web Template has two main layouts for a listing page. One layout features a single cropped image on top of the page, which is more geared toward rentals and services. The other layout features a full image of the item, which is designed with product marketplaces in mind.

### Availability

Listings can feature an availability calendar. You can configure the availability of your listings to be nightly, daily, or hourly. With code modifications, you can offer bookings using custom time increments (for instance, 15-minute intervals). You can also offer multiple different booking length options (for instance, hourly and daily booking options).

Sellers add their availability when creating their listing. Daily and nightly availability lets them add a default weekly schedule and easily set any exceptions to that default schedule. Hourly availability lets providers set their exact schedule for every day up to one year in advance.

Customers book from the provider's availability calendar on the listing page.

The Sharetribe Web Template has time zone support. The seller's location determines the timezone of the listing, and that timezone is saved into the listing's availability plan. Bookings can be made for multiple spots or seats. This feature can be added easily by modifying the Template code.

Read more about [availability management in Sharetribe](/docs/references/availability/).

### Inventory management

On the other hand, listings can be about products and offer inventory management. The seller adds the number of items they have in stock during product listing creation. When a customer makes a purchase, that number is automatically reduced. The seller can manage their stock manually by editing the listing if there are inventory changes that are not related to transactions.

You can configure the Template so that the default inventory for listings is always one (1). When an item is sold, its stock will automatically go to zero. Listings with zero stock are filtered out by default, and do not appear in search.

Read more about [inventory management in Sharetribe](/docs/concepts/inventory-management/).

## Search

### Keyword or location search

Two search options are available: search by keyword or by location. Keyword search lets customers enter their queries via a search bar. Customers can further refine their search using sorting and filtering options available in a left-hand toolbar.

Location search lets customers type in a specific address, with listings returned in order of their distance from the searched-for location. The search page for location search includes a map; customers can scroll the map to find new listings from different locations. Sorting and filtering fields are available via a drop down menu near the top of the page.

Learn more about how to [configure your search settings](/docs/template/configuration/#search-configuration).

### Browsing and filtering

You can build the filtering experience of your marketplace using configurable extended data. Simple editing of a JSON schema allows adding, removing, or modifying what fields are added to listings. Set the fields' read and write permissions depending on the needed use case, and quickly turn listing fields into corresponding filters.

Read more about how to [configure extended data in the Sharetribe Web Template](/docs/template/configuration/#listing-configuration).

## Transactions

### Booking default process

Sharetribe Web Template offers transactions with calendar bookings, where customers book time from a provider's availability calendar. The price they pay equals the amount of time booked multiplied by the booking price. Customers can book by days, nights, or hours.

Providers add their availability while creating their listings. They configure when they are available for booking, creating a calendar that the customer can use during search and booking.

### Product default process

You can also configure the Sharetribe Web Template to become a marketplace for purchasing. The calendar booking is removed; purchasing happens by selecting the number of units. Shipping is offered by sellers, who set a fixed shipping price for the first and any subsequent items purchased.

Units may be purchased from an available stock, which is set by the seller. Out of stock listings cannot be purchased.

### Payments

Customers pay using credit card or debit card by entering their payment information on the checkout page. Payments in the product default process are captured immediately, whereas payments with the booking default process are only captured after the provider accepts the booking request. In either case, funds are transferred to the marketplace's Stripe account. The configured seller and customer commissions are paid out to the marketplace. The remaining funds (the seller's earnings) are paid out to their bank account according to rules of the transaction process.

Learn more about [how payments work in Sharetribe](/docs/concepts/payments-overview/).

### Messaging

Sharetribe Web Template allows sellers and customers to send private messages back and forth. Users can start a message thread with another user from their profile or their listing. Users access messages in their inbox. Each message thread also includes a timeline of different actions taken during the transaction. Messages initiate transactions, but do not transition a transaction from one state to another.

### Reviews

Once a transaction has completed, the customer and provider can both leave reviews. In Sharetribe Web Template, reviews remain hidden until either both parties have submitted their reviews or the review period expires. This ensures that the second participant's review is not affected by the first review. Both reviews are published to both parties at the same time, and after the review is published it cannot be modified anymore.

## Account management

### Signup and login

The Sharetribe Web Template allows anonymous users (i.e. users without an account) to visit content pages, search, and browse listings. Most every other action requires users to create an account on the signup page with their email and password. After confirming their email, signed up users can engage in selling, buying, or both.

Users can login to their accounts using their email and password. They can reset their passwor