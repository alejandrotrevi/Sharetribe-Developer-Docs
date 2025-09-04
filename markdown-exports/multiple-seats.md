```markdown
---
title: "Add Listings with Multiple Seats"
source: "provided"
extracted_date: "2024-12-04"
summary: "This guide describes how to handle multiple seats in listings and bookings in earlier template versions."
---

- [Table of Contents](#table-of-contents)
- [Introduction](#introduction)
- [Update EditListingAvailabilityPanel to add seats to listings](#update-editlistingavailabilitypanel-to-add-seats-to-listings)
- [Add seats handling to default availability plan](#add-seats-handling-to-default-availability-plan)
- [Add seats handling to EditListingAvailabilityExceptionForm](#add-seats-handling-to-editlistingavailabilityexceptionform)
- [Show seats input in order form](#show-seats-input-in-order-form)
- [Calculate price with seats](#calculate-price-with-seats)
- [Allow bookings with multiple seats](#allow-bookings-with-multiple-seats)
- [Recap](#recap)

# Introduction
This guide describes how to handle multiple seats in listings and bookings in earlier template versions.

## Last updated 
*December 4, 2024*

---

## Update EditListingAvailabilityPanel to add seats to listings

Seats are a feature of a bookable listing’s availability plan. By default, all listings are created with availability plans featuring one seat.

To enable booking listings with multiple seats, we need to add the ability to create listings whose availability plan contains more than one seat. We will make changes in EditListingAvailabilityPanel:
- For enabling seats in the default plan, update **AvailabilityPlanEntries.js**.
- For managing seats in availability exceptions, update **EditListingAvailabilityExceptionForm.js**.
- For saving those changes, update in **EditListingAvailabilityPanel.js**.
- For showing those changes, update in **WeeklyCalendar.js**.

```shell
└── src
    └── containers
        └── EditListingPage
            └── EditListingWizard
                └── EditListingAvailabilityPanel
                    ├── EditListingAvailabilityExceptionForm
                    │   ├── EditListingAvailabilityExceptionForm.js
                    │   └── EditListingAvailabilityExceptionForm.module.css
                    ├── EditListingAvailabilityPlanForm
                    │   └── AvailabilityPlanEntries.js
                    ├── WeeklyCalendar
                    │   └── WeeklyCalendar.js
                    └── EditListingAvailabilityPanel.js
```

## Add seats handling to default availability plan

**AvailabilityPlanEntries** is a component representing a single day’s availability in **EditListingAvailabilityPlanForm**.

```shell
└── src
    └── containers
        └── EditListingPage
            └── EditListingWizard
                └── EditListingAvailabilityPanel
                    └── EditListingAvailabilityPlanForm
                        └── AvailabilityPlanEntries.js
```

We need to show an input for the number of seats in **AvailabilityPlanEntries**.

### Show seats input in AvailabilityPlanEntries

Import **FieldTextInput** from **src/components**:

```js
import {
  InlineTextButton,
  IconClose,
  FieldSelect,
  FieldCheckbox,
  FieldTextInput, // add this row
} from '../../../../../components';
```

Replace the **TimeRangeHidden** component with:
```jsx
const TimeRangeHidden = (props) => {
  const { name, value, onChange, intl } = props;
  return (
    <div>
      <div className={css.formRowHidden}>
        <FieldHidden name={`${name}.startTime`} />
        <FieldHidden name={`${name}.endTime`} />
      </div>
      <FieldTextInput
        name={`${name}.seats`}
        type="number"
        initialValue={value.seats}
        placeholder={intl.formatMessage({ id: 'EditListingAvailabilityPlanForm.seatsPlaceholder' })}
        min="1"
        onChange={onChange}
      />
    </div>
  );
};
```

### Add seats handling to EditListingAvailabilityExceptionForm

In **EditListingAvailabilityExceptionForm.js**, import **FieldTextInput** for seats handling.

Add the seats selection input similarly to **EditListingAvailabilityPanel.js**. Validate the input.

## Show seats input in order form

In **BookingDatesForm.js**:

```shell
└── src
    └── components
        └── OrderPanel
            └── BookingDatesForm
                └── BookingDatesForm.js
```

Add functions to get minimum seats:
```js
const pickBookingMonthTimeSlots = (monthlyTimeSlots, startDate, endDate, timeZone) => {
  const monthsInRange = generateMonths(startDate, endDate, timeZone);
  return monthsInRange.reduce((timeSlots, firstOfMonth) => {
    return [
      ...timeSlots,
      ...pickMonthlyTimeSlots(monthlyTimeSlots, firstOfMonth, timeZone),
    ];
  }, []);
};
```

## Calculate price with seats

In **lineItems.js**, add:

```js
const getDateRangeUnitsSeatsLineItems = (orderData, code) => {
  const { bookingStart, bookingEnd, seats } = orderData;
  const units = bookingStart && bookingEnd ? calculateQuantityFromDates(bookingStart, bookingEnd, code) : null;
  return { units, seats, extraLineItems: [] };
};
```

## Final steps: Update CheckoutPage

In **CheckoutPageWithPayment.js**, update how **seats** are handled:
```diff
const getOrderParams = (pageData, shippingDetails, optionalPaymentParams, config) => {
  const quantity = pageData.orderData?.quantity;
  const quantityMaybe = quantity ? { quantity } : {};
  const seats = pageData.orderData?.seats;
  const seatsMaybe = seats ? { seats } : {};
  ...
  const orderParams = {
    listingId: pageData?.listing?.id,
    ...deliveryMethodMaybe,
    ...quantityMaybe,
    ...seatsMaybe,
    ...bookingDatesMaybe(pageData.orderData?.bookingDates),
    ...protectedDataMaybe,
    ...optionalPaymentParams,
  };
  return orderParams;
};
```

## Recap

In this guide, you made the following changes:

- Added seats to listings.
- Updated availability plan handling.
- Showed seats selection in the order panel.
- Calculated line items with seats.
- Allowed bookings with multiple seats.

If you have any questions, feel free to reach out to our support team or consult the Sharetribe developer community.
```