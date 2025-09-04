---
title: "Implement user deletion"
source: "provided"
extracted_date: "2025-01-13"
summary: "This article guides you in implementing a feature where a user can delete their own account through the Sharetribe Web Template Account settings section."
---

- [Add UI components](#add-ui-components)
  - [Add "Delete account" tab to Account settings](#add-delete-account-tab-to-account-settings)
  - [Add DeleteAccountForm](#add-deleteaccountform)
- [Add logic to call the Sharetribe API endpoint](#add-logic-to-call-the-sharetribe-api-endpoint)
  - [Update SDK](#update-sdk)
  - [Add endpoint to client side](#add-endpoint-to-client-side)
  - [Add endpoint logic](#add-endpoint-logic)
- [Test functionality](#test-functionality)

# Implement user deletion

This article guides you in implementing a feature where a user can delete their own account through the Sharetribe Web Template Account settings section.

Sharetribe Marketplace API has an endpoint for [deleting a user's account](https://www.sharetribe.com/api-reference/marketplace.html#delete-user). After the user has been deleted, any transactions where they are a participant cannot transition forward, and no payouts will be made to the user – even if the payouts have already been scheduled.

This how-to article guides you in implementing an account deleting feature in the Sharetribe Web Template "Account settings" section. When the signed-in user navigates to the account deletion page, they need to enter their password to confirm that they do in fact want to delete their account. An endpoint in the template application's server then gets called, and that endpoint checks whether the user has incomplete transactions.

Depending on the result, the endpoint then either returns the count of incomplete transactions, or deletes the user's account through the `current_user/delete` endpoint. If the user cannot be deleted, the page shows the count of unfinished transactions, and if the user is deleted, they are immediately logged out.

This how-to guide is based on the Sharetribe Web Template and the `default-booking` process. As you implement the guide, you will need to review the transaction processes used on your marketplace to see which transitions count as non-final transitions, i.e. ones where you do not want to allow the user to delete their profile.

![Non-final transitions in default-booking process](/docs/static/7bbdae402fe6db6c9180bbc69b28dbc9/53fb6/non-final-transitions.png)

In addition to incomplete transactions, your marketplace transaction processes may have payout considerations. If your transaction process uses the default Stripe integration including payouts, it may take up to 7 days from `stripe-confirm-payment-intent` action until the payout is processed by Stripe, even if `stripe-create-payout` is called and the payout has been scheduled.

In other words, if your transaction processes create a provider payout close to the payment intent being confirmed, you may want to prevent users from deleting their profile before 7 days have passed from payment intent being confirmed. Alternatively, you can of course handle payouts manually for deleted users, if the transactions have otherwise been completed.

## Add UI components

### Add "Delete account" tab to Account settings

First, add a new tab to the Account settings section of the template.

```shell
└── src
    └── components
        └── LayoutComposer
            └── LayoutSideNavigation
                └──LayoutWrapperAccountSettingsSideNav.js
          ...
```

The new page will appear as the final tab in Account Settings side navigation.

```jsx
      linkProps: {
        name: 'PaymentMethodsPage',
      },
    },
    {
      text: <FormattedMessage id="LayoutWrapperAccountSettingsSideNav.deleteAccountTabTitle" />,
      selected: currentPage === 'DeleteAccountPage',
      id: 'DeleteAccountPageTab',
      linkProps: {
        name: 'DeleteAccountPage',
      },
    },
  ];
```

Next, add the actual DeleteAccountPage component. You can use the example files directly, or modify them for your use case.

```shell
└── src
    └── containers
         └── DeleteAccountPage
              └── DeleteAccountPage.js
              └── DeleteAccountPage.duck.js
              └── DeleteAccountPage.module.css
```

- [DeleteAccountPage.js](https://www.sharetribe.com/docs/703635f4f59fb113deaec3089a739ae3/DeleteAccountPage.js)
- [DeleteAccountPage.duck.js](https://www.sharetribe.com/docs/db3c8388f7dce4686cadfc5b4dd29be0/DeleteAccountPage.duck.js)
- [DeleteAccountPage.module.css](https://www.sharetribe.com/docs/bf040c46e02239f39fe01ee9e5659810/DeleteAccountPage.module.css)

Once the DeleteAccountPage component and corresponding Redux store DeleteAccountPage.duck.js exist in the project, import the Redux file reducer to a combined reducer.

```shell
└── src
    └── containers
         └── reducers.js
```

For more info on the Redux structure, you can read about [the Ducks modular Redux proposal](https://github.com/erikras/ducks-modular-redux), which is the proposal of Redux usage followed in the Sharetribe Web Template.

```js
  import ContactDetailsPage from './ContactDetailsPage/ContactDetailsPage.duck';
  import DeleteAccountPage from './DeleteAccountPage/DeleteAccountPage.duck';
  import EditListingPage from './EditListingPage/EditListingPage.duck';
  ...

  export {
    CheckoutPage,
    ContactDetailsPage,
    DeleteAccountPage,
    EditListingPage,
```

Since DeleteAccountPage has a new route, add the component to the application's route configuration.

```shell
└── src
    └── routing
        └── routeConfiguration.js
```

```js
const ContactDetailsPage = loadable(() =>
  import(
    /* webpackChunkName: "ContactDetailsPage" */ '../containers/ContactDetailsPage/ContactDetailsPage'
  )
);
const DeleteAccountPage = loadable(() =>
  import(
    /* webpackChunkName: "DeleteAccountPage" */ '../containers/DeleteAccountPage/DeleteAccountPage'
  )
);
const EditListingPage = loadable(() =>
  import(
    /* webpackChunkName: "EditListingPage" */ '../containers/EditListingPage/EditListingPage'
  )
);
```

The "Account settings" side navigation tabs are defined in routeConfiguration.js as well, so add DeleteAccountPage in the tab array.

```js
export const ACCOUNT_SETTINGS_PAGES = [
  'ContactDetailsPage',
  'PasswordChangePage',
  'StripePayoutPage',
  'PaymentMethodsPage',
  'DeleteAccountPage',
];
```

Then, add the delete profile route to the array returned by the route configuration function:

```js
    {
      path: '/account/delete-profile',
      name: 'DeleteAccountPage',
      auth: true,
      authPage: 'LoginPage',
      component: DeleteAccountPage,
    },
```

Finally, add the necessary marketplace text rows into either src/translations/en.json or the [Sharetribe Console Marketplace texts editor](https://www.sharetribe.com/docs/concepts/marketplace-texts/).

```shell
└── src
    └── translations
         └── en.json
```

Feel free to modify the texts to suit your marketplace.

```text
"DeleteAccountPage.details": "This action is permanent and cannot be undone! After deleting your account, you will be logged out immediately and will not be able to access your listings or transactions anymore.",
"DeleteAccountPage.heading": "Delete your account",
"DeleteAccountPage.title": "Delete your account",
"DeleteAccountPage.error": "Cannot delete user profile. You have {errorCause} Please contact Biketribe support or complete the unfinished issues and try again.",
"LayoutWrapperAccountSettingsSideNav.deleteAccountTabTitle": "Delete account",
```

If you now navigate to Account Settings, you can see the "Delete account" tab on the left side navigation. If you click the tab, you can see the info text.

![Initial Delete Account page](/docs/static/151f42ad7a77c6d9a5dcfc016ae20647/53fb6/delete-account-page.png)

Next, we'll implement