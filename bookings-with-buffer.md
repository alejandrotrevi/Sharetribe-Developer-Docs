---
title: "Add buffer time to bookings in time-based listings"
source: "provided"
extracted_date: "2025-01-18"
summary: "This guide describes how to modify booking times in time-based listings to have a buffer after the time slots"
---

- [Add a 15 minute buffer between one hour bookings](#add-a-15-minute-buffer-between-one-hour-bookings)
- [Allow users to book buffered appointments at non-sharp hours](#allow-users-to-book-buffered-appointments-at-non-sharp-hours)

# Add buffer time to bookings in time-based listings

This guide describes how to modify booking times in time-based listings to have a buffer after the time slots

For some services booked by the hour, the provider may want to reserve a buffer time between bookings. For instance a bike rental agency may want to check their bikes between rentals, or a massage therapist may need to clear up and restock their therapy space before the next customer arrives. You can set your marketplace to add these kinds of buffers by default, so providers do not need to add availability exceptions manually.

## Add a 15 minute buffer between one hour bookings

The simplest use case for buffered bookings is having one hour slots that are bookable at the top of the hour, and adding 15 minutes of unbookable time after each slot. To achieve that, we will use the booking's display time attribute.

First, we will add a helper function in _src/util/dates.js_ file to add the correct buffer time.

```shell
└── src
    └── util
        └── dates.js
```

```diff
+ const bufferMinutes = 15;

+ export const addBuffer = (date) => moment(date).add(bufferMinutes, 'minutes').toDate();
```

### Add display end time handling

The user can select one hour booking slots on the listing page. When an order gets initiated, we want to set the selected end time as the display time, and a new buffered end time as the actual booking time. This way, the booking extends over both the customer's booking time and the buffer, so that other customers can not book over the buffer. We use the newly created _addBuffer_ function to extend the display end time.

```shell
└── src
    └── containers
        └── CheckoutPage
            └── CheckoutPage.duck.js
```

```diff
+ import { addBuffer } from '../../util/dates';
 ...

export const initiateOrder = (orderParams, transactionId) => (dispatch, getState, sdk) => {
  dispatch(initiateOrderRequest());
...
  const quantityMaybe = quantity ? { stockReservationQuantity: quantity } : {};

- const bookingParamsMaybe = bookingDates || {};
+ let bookingParamsMaybe = {};
+
+ if (bookingDates) {
+   bookingParamsMaybe = {
+     ...bookingDates,
+     bookingEnd: addBuffer(bookingDates.bookingEnd),
+     bookingDisplayEnd: bookingDates.bookingEnd,
+     bookingDisplayStart: bookingDates.bookingStart,
+   }
+ }

  // Parameters only for client app's server
  const orderData = deliveryMethod ? { deliveryMethod } : {};
...
```

We also need to modify line item calculation. By default, the transaction's price is calculated based on the booking's start and end moments, however in this case we want to use the display end attribute for the price calculation.

```shell
└── server
    └── api-util
        └── lineItems.js
```

We have already passed _displayEnd_ in _bookingData_, so we just need to add handling for it in _lineItems.js_. Since the function is used both with and without display end time, e.g. when calling _api/transaction-line-items_, we need to accommodate both use cases.

The _lineItems.js_ file has a helper function for calculating hourly booking quantity, so we can modify it directly.

```diff
const getHourQuantityAndLineItems = orderData => {
- const { bookingStart, bookingEnd } = orderData || {};
+ const { bookingStart, bookingEnd, bookingDisplayEnd } = orderData || {};
+ const end = bookingDisplayEnd ?? bookingEnd;
  const quantity =
-   bookingStart && bookingEnd ? calculateQuantityFromHours(bookingStart, bookingEnd) : null;
+   bookingStart && end ? calculateQuantityFromHours(bookingStart, end) : null;

  return { quantity, extraLineItems: [] };
};
```

### Set getStartHours to return correct available start times

Finally, we need to make sure that the start time slots only show valid start times including the buffer – if someone has booked 6pm to 7pm, then another customer cannot book 5pm to 6pm because there is not enough availability for both the time slot and the buffer.

```shell
└── src
    └── util
        └── dates.js
```

First, add a constant for a full hour in addition to the buffer time.

```diff
  const bufferMinutes = 15;
+ const hourMinutes = 60;
```

Then, fix _getStartHours_ handling. By default, start hours and end hours are determined from the same list, so _getStartHours_ removes the final list item to allow for a single hour between the final start and end times.

However, when the actual booking slot is longer than the displayed one, we need to make sure that the interval between the final start and end times can fit a buffered booking. Therefore, instead of removing a single start time to fit a one hour booking slot, we remove as many start times as it takes to fit a buffered hour before the final end time.

```diff
export const getStartHours = (startTime, endTime, timeZone, intl) => {
  const hours = getSharpHours(startTime, endTime, timeZone, intl);
- return hours.length < 2 ? hours : hours.slice(0, -1);
+ const removeCount = Math.ceil((hourMinutes + bufferMinutes) / hourMinutes);
+ return hours.length < removeCount ? [] : hours.slice(0, -removeCount);
};
```

## Allow users to book buffered appointments at non-sharp hours

Having this kind of setup then means that the slots are, in practice, bookable every two hours. If we do not want to leave extra gaps between bookings beyond the buffer, we can set start times to repeat on the buffer time cadence instead of hourly. That way, if someone books the 8 AM - 9 AM slot, the next available start time is 9.15 AM i.e. right after the buffer.

In other words, we need to have start and end times at a different cycle:

- start times at 15 minute increments i.e. a customer can start their booking at any quarter hour, instead of at the top of the hour only
- end times at one hour increments, i.e. the customer can book one or more full hours only.

Time slot handling is done using a few helper functions in src/util/dates.js

```shell
└── src
    └── util
        └── dates.js
```

- _getStartHours_ and _getEndHours_ return a list of timepoints that are displayed as the booking's possible start and end moments, respectively. They both use the same helper function _getSharpHours_
- _getSharpHours_ retrieves the sharp hours that exist within the availability time slot. It uses the _findBookingUnitBoundaries_ function.
- _findBookingUnitBoundaries_ is a recursive function that checks whether the current boundary (e.g. sharp hour) passed to it falls within the availability time slot.
  - If the current boundary is within the availability time slot, the function calls itself with the next boundary and cumulates the boundary results into an array.
  - If the current boundary does not fall within the availability time slot, the function returns the cumulated results from the previous iterations.
  - _findBookingUnitBoundaries_ takes a _nextBoundaryFn_ parameter that it uses to determine the next boundary value to pass to itself.
- the function passed as _nextBoundaryFn_ by default is _findNextBoundary_. The _findNextBoundary_ function increments the current boundary by a predefined value.

```js
export const findNextBoundary = (timeZone, currentMomentOrDate) =>
  moment(currentMomentOrDate)
    .clone()
    .tz(timeZone)
    .add(1, 'hour') // The default handling