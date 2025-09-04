```markdown
---
title: "Change Transaction Process in Sharetribe Web Template"
source: "provided"
extracted_date: "2023-10-24"
summary: "This guide describes how to customize Sharetribe Web Template to use a new transaction process."
---

- [Table of Contents](#table-of-contents)
  - [1. Add the new transaction process configuration](#1-add-the-new-transaction-process-configuration)
  - [2. Update the relevant files in `src/transactions` folder](#2-update-the-relevant-files-in-srctransactions-folder)
    - [2.1. Update transitions and states](#21-update-transitions-and-states)
    - [2.2. Update state graph to match the new transaction process](#22-update-state-graph-to-match-the-new-transaction-process)
    - [2.3. Update graph helper functions to match the new process](#23-update-graph-helper-functions-to-match-the-new-process)
  - [3. Update state data for Inbox Page and Transaction Page](#3-update-state-data-for-inbox-page-and-transaction-page)
  - [4. Add marketplace text strings](#4-add-marketplace-text-strings)

# Change Transaction Process in Sharetribe Web Template

This guide describes how to customize Sharetribe Web Template to use a new transaction process.

## 1. Add the New Transaction Process Configuration

The [`src/config/configListing.js`](https://github.com/sharetribe/web-template/blob/main/src/config/configListing.js) file lists the listing types actively used by the template, as well as the transaction processes related to those types. You need to add a configuration for a new transaction process. You can either comment out the previous active process definitions or leave them as they are.

```javascript
{
    listingType: 'nightly-booking',
    label: 'Nightly booking',
    transactionType: {
        process: 'negotiated-booking',
        alias: 'negotiated-booking/release-1',
        unitType: 'night',
    },
}
```

## 2. Update the Relevant Files in `src/transactions` Folder

Supported transaction processes are also defined in the files found in the `src/transactions` folder. 

```
└── src
    └── transactions
        └── transaction.js
```

```javascript
// Then names of supported processes
export const PURCHASE_PROCESS_NAME = 'default-purchase';
export const BOOKING_PROCESS_NAME = 'default-booking';
// Add new processes with a descriptive name
export const NEGOTIATION_PROCESS_NAME = 'negotiated-booking';
```

If you are replacing one of the default processes, modify the existing `transactionProcessBooking.js` file to correspond to the new process.

### 2.1 Update Transitions and States

If the new transaction process has different transitions and states, you should add or remove those.

### 2.2 Update State Graph to Match the New Transaction Process

State graph description makes it easier to understand how the transaction process works.

```javascript
export const graph = {
  id: 'default-booking/release-1',
  initial: states.INITIAL,
  states: {
    [states.INITIAL]: {
      on: {
        [transitions.INQUIRE]: states.INQUIRY,
        [transitions.REQUEST_PAYMENT]: states.PENDING_PAYMENT,
      },
    },
    // etc.
  },
};
```

### 2.3 Update Graph Helper Functions to Match the New Process

Since the states and transitions in your state graph description have changed, review all the helper functions in your transaction process file and adjust them accordingly.

```javascript
export const isPrivileged = transition => {
  return [
    transitions.REQUEST_PAYMENT,
    transitions.REQUEST_PAYMENT_AFTER_INQUIRY,
  ].includes(transition);
};
```

## 3. Update State Data for Inbox Page and Transaction Page

In addition to the transaction process file, transaction process state data is handled in `InboxPage` and `TransactionPage`.

```
└── src
    └── containers
        ├── InboxPage
            ├── InboxPage.stateData.js
            ├── InboxPage.stateDataBooking.js
            └── InboxPage.stateDataPurchase.js
        └── TransactionPage
            ├── TransactionPage.stateData.js
            ├── TransactionPage.stateDataBooking.js
            └── TransactionPage.stateDataPurchase.js
```

### Exporting Functions for Conditional Rendering

```javascript
export const getStateData = params => {
  if (processName === PURCHASE_PROCESS_NAME) {
    return getStateDataForPurchaseProcess(params, processInfo());
  } else if (processName === BOOKING_PROCESS_NAME) {
    return getStateDataForBookingProcess(params, processInfo());
  }
};
```

## 4. Add Marketplace Text Strings

Many marketplace text strings in Sharetribe Web Template are transaction process and state specific.

```javascript
"InboxPage.default-booking.accepted.status": "Accepted",
"InboxPage.default-booking.canceled.status": "Canceled",
// etc.
```

You will need to add microcopy for the relevant keys and states in the new process. You can add the process-specific keys either into the [bundled marketplace text files in the template](https://docs.sharetribe.com/template/how-to-change-bundled-marketplace-texts) or through the [Sharetribe Console Content tab](https://docs.sharetribe.com/concepts/marketplace-texts).

After making the necessary changes, your new transaction process should work as expected in the template!
```