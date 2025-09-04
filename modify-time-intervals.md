---
title: "Modify booking time intervals in time-based listings"
source: "provided"
extracted_date: "2025-01-07"
summary: "This guide describes how to modify booking time intervals in hourly listings."
---

- [Set booking length to 30 minutes](#set-booking-length-to-30-minutes)
- [Use an irregular time slot](#use-an-irregular-time-slot)
- [Add separate handling for first timeslot](#add-separate-handling-for-first-timeslot)

# Modify booking time intervals in time-based listings

This guide describes how to modify booking time intervals in hourly listings.

In Sharetribe, listings can have either day-based or time-based availability. For listings with time-based availability, the available [time slots](https://www.sharetribe.com/api-reference/marketplace.html#time-slots) are returned from the API as continuous stretches of time. The client application must therefore split the availability into suitable booking lengths.

The default behavior of the Sharetribe Web Template hourly listings is to split the continuous availability stretch into one hour bookable intervals. This how-to guide illustrates a handful of different cases on modifying booking lengths. These changes apply to all listings where the unit type is set to "hour".

> **Information**
>
> Starting with Sharetribe Web Template [v8.0.0](https://github.com/sharetribe/web-template/releases/tag/v8.0.0), the template also supports fixed timeslots, where the provider can choose the length of possible bookings, and the customer only selects a start time for their booking. This guide applies to non-fixed time slots, where the customer can select multiple intervals of the available booking length by setting a start time and an end time.

## Set booking length to 30 minutes

The simplest use case is to create uniform 30 minute booking slots. Start with adding a constant for the booking length in minutes.

```js
const timeSlotMinutes = 30;
```

Time slot handling is done using a few helper functions in src/util/dates.js

```shell
└── src
    └── util
        └── dates.js
```

- *getStartHours* and *getEndHours* return a list of timepoints that are displayed as the booking's possible start and end moments, respectively. They both use the same helper function *getSharpHours*
- *getSharpHours* retrieves the sharp hours that exist within the availability time slot. It uses the *getBoundaries* function and passes in hard-coded 1 hour time increments.
- The *getBoundaries* function fetches the boundaries for bookable slots based on a given time unit. It calls the *findBookingUnitBoundaries* function.
- *findBookingUnitBoundaries* is a recursive function that checks whether the current boundary (e.g. sharp hour or custom time span) passed to it falls within the availability time slot.
  - If the current boundary is within the availability time slot, the function calls itself with the next boundary and cumulates the boundary results into an array.
  - If the current boundary does not fall within the availability time slot, the function returns the cumulated results from the previous iterations.
  - *findBookingUnitBoundaries* takes a *nextBoundaryFn* parameter that it uses to determine the next boundary value to pass to itself.
- the function passed as *nextBoundaryFn* is *findNextBoundary*. The *findNextBoundary* function increments the current boundary by a predefined value, both for custom time units and for the default ones – day and hour.

```js
/**
 * This is the default behavior of the template
 */
export const findNextBoundary = (
  currentDate,
  unitCount,
  timeUnit,
  timeZone
) => {
  const customTimeUnitConfig = bookingTimeUnits[timeUnit]?.isCustom
    ? bookingTimeUnits[timeUnit]
    : null;

  if (!!customTimeUnitConfig) {
    // If the time unit is custom, we need to use startOfMinuteBasedInterval function to adjust 00, 15, 30, 45 rounding.
    const customTimeUnitInMinutes =
      customTimeUnitConfig?.timeUnitInMinutes;
    const minuteOffset = !!customTimeUnitInMinutes
      ? unitCount * customTimeUnitInMinutes
      : unitCount;

    return moment(currentDate)
      .clone()
      .tz(timeZone)
      .add(minuteOffset, 'minute')
      .startOfMinuteBasedInterval(customTimeUnitInMinutes)
      .toDate();
  } else {
    // Other time units are handled with the default moment.js functions
    return moment(currentDate)
      .clone()
      .tz(timeZone)
      .add(unitCount, timeUnit)
      .startOf(timeUnit)
      .toDate();
  }
};
```

### Use the custom extension function for moment.js

The template hourly listing handling uses the [moment-timezone](https://momentjs.com/timezone/) library to modify times and dates and convert them between the listing's time zone and the user's time zone.

By default, the *findNextBoundary* function uses *moment.startOf(timeUnit)* to round the booking slots to the top of each hour or day. For custom time slots, the function uses customized moment.js extension *startOfMinuteBasedInterval()*, which is defined earlier in the same dates.js file.

```js
moment.fn.startOfMinuteBasedInterval = function(unitCount) {
  const durationInMs = moment
    .duration(unitCount, 'minutes')
    .asMilliseconds();

  // Calculate the number of durations since 1970-01-01 00:00:00
  const durationCount = Math.floor(this.valueOf() / durationInMs);
  // Return a moment that is rounded to the start of the previous whole number of durations
  return moment(durationCount * durationInMs);
};
```

It is added to *moment.js* using the prototype exposed through *moment.fn*, so that we can chain it in the same place as the default *startOf(timeUnit)* function.

### Add a new block for handling customized time slots

You will need to create a new *else if* block for the hourly unit type, where you use the *startOfMinuteBasedInterval* function instead of the default *startOf(timeUnit)* function, and pass the *timeSlotMinutes* value as the addition and rounding duration values.

```diff
export const findNextBoundary = (
  currentDate,
  unitCount,
  timeUnit,
  timeZone
) => {
  const customTimeUnitConfig = bookingTimeUnits[timeUnit]?.isCustom
    ? bookingTimeUnits[timeUnit]
    : null;

  if (!!customTimeUnitConfig) {
    // If the time unit is custom, we need to use startOfMinuteBasedInterval function to adjust 00, 15, 30, 45 rounding.
    const customTimeUnitInMinutes =
      customTimeUnitConfig?.timeUnitInMinutes;
    const minuteOffset = !!customTimeUnitInMinutes
      ? unitCount * customTimeUnitInMinutes
      : unitCount;

    return moment(currentDate)
      .clone()
      .tz(timeZone)
      .add(minuteOffset, 'minute')
      .startOfMinuteBasedInterval(customTimeUnitInMinutes)
      .toDate();
+ } else if (timeUnit === 'hour') {
+   // Replace hourly handling with the defined booking length
+   return moment(currentDate)
+     .clone()
+     .tz(timeZone)
+     .add(timeSlotMinutes, 'minute')
+     .startOfMinuteBasedInterval(timeSlotMinutes)
+     .toDate();
  } else {
    // Other time units are handled with the default moment.js functions
    return moment(currentDate)
      .clone()
      .tz(timeZone)
      .add(unitCount, timeUnit)
      .startOf(timeUnit)
      .toDate();
  }
};
```

For listings with an hourly price, the server-side function *calculateQuantityFromHours* in server/api-util/lineItemHelpers.js determines the correct quantity as a decimal of full hours. However, if you want to set a