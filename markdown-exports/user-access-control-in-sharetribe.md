```markdown
---
title: "User Access Control"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2024-10-26"
summary: "This article explains what types of user access control are available in a Sharetribe marketplace."
---

- [Table of Contents](#table-of-contents)
- [Introduction](#introduction)
- [Make marketplace private](#make-marketplace-private)
- [Approve users who want to join](#approve-users-who-want-to-join)
- [Restrict listing posting rights](#restrict-listing-posting-rights)
- [Restrict transaction rights](#restrict-transaction-rights)
- [Restrict viewing rights](#restrict-viewing-rights)
- [API level restrictions](#api-level-restrictions)
- [Permissions in the access-control.json asset](#permissions-in-the-access-controljson-asset)
- [Permissions in the currentUser resource](#permissions-in-the-currentuser-resource)
- [Conclusion](#conclusion)

# Introduction
This article explains what types of user access control are available in a Sharetribe marketplace.

## Make marketplace private
By default, Sharetribe marketplaces are public. This means that listings and user profiles are visible to unauthenticated users. By making your marketplace private, you allow only authenticated users to view listings and users on your marketplace.

On a private marketplace, the only public Marketplace API endpoints are user creation and password reset related. All other endpoints require an authenticated user access token.

This setting can be turned on or off on the marketplace level. See which endpoints are affected by this setting [here](#making-marketplace-private). 

Read more about this feature in the [Help Center](https://www.sharetribe.com/help/en/articles/9503164-make-marketplace-private).

## Approve users who want to join
User approval means that when a user signs up, they need to be approved by an admin before they have full access to the marketplace.

On **public marketplaces**, this means that users pending approval can view listings and other users' profiles, but they cannot post listings or initiate transactions.

On **private marketplaces**, users pending approval can only view their own profile – they cannot view any other marketplace data.

You can enable this setting in the Access Control tab. See which endpoints are affected by this setting [here](#approve-users-who-want-to-join-1). 

Read more about this feature in the [Help Center](https://www.sharetribe.com/help/en/articles/9503152-approve-users-who-want-to-join).

## Restrict listing posting rights
You might also want to limit listing posting rights to certain users only. 

For example, if you have user types "Buyer" and "Seller," you might want to grant listing posting rights to sellers only. Or you might be monetizing your marketplace with subscriptions, and therefore only want to grant posting rights to users who have subscribed.

On the marketplace level, you can toggle the selection in the Access control tab. See which endpoints are affected by this setting [here](#restrict-posting-rights).

Read more about this feature in the [Help Center](https://www.sharetribe.com/help/en/articles/9503118-restrict-listing-publishing-rights).

## Restrict transaction rights
You might also want to restrict the ability to initiate transactions to specific users only. 

For example, if you have a marketplace where users can view listings but need to verify their identity before making purchases, you can enable this feature to manually control who can initiate transactions. 

You can toggle the setting in the Access control tab. See which endpoints are affected by this setting [here](#restrict-transaction-rights-1).

Read more about this feature in the [Help Center](https://www.sharetribe.com/help/en/articles/9790336).

## Restrict viewing rights
On private marketplaces, you can restrict viewing rights for individual users. 

If a user's viewing rights have been restricted, they will only see their own listings and profile even if they have previously been approved. You can toggle the setting in the Access control tab when the marketplace has been set to private.

See which endpoints are affected by this setting [here](#restrict-viewing-rights-1).

Read more about this feature in the [Help Center](https://www.sharetribe.com/help/en/articles/9790341).

## API level restrictions
Toggling permissions under the "Access Control" tab in the Console directly impacts API behavior. 

When permissions are modified, endpoint access is adjusted accordingly, ensuring that users cannot bypass the restrictions imposed by these settings. This guarantees access control policies are strictly enforced.

These restrictions only apply in the Marketplace API. Marketplace end-users do not access the Integration API directly.

## Permissions in the access-control.json asset
On the marketplace level, the changes made in Console get recorded in the asset `/general/access-control.json`.

```json
{
  "id": "66cc804f-b5ee-44f2-8c7e-ca3e950e534f",
  "type": "jsonAsset",
  "attributes": {
    "assetPath": "/general/access-control.json",
    "data": {
      "marketplace": {
        "private": false
      },
      "users": {
        "requireApprovalToJoin": false,
        "requireApprovalToJoinOptions": {
          "callToAction": {
            "type": "internal",
            "text": "Add a link to request approval to join",
            "href": "/p/about/"
          }
        },
        "requirePermissionToPostListings": true,
        "requirePermissionToPostListingsOptions": {
          "callToAction": {
            "type": "none"
          }
        },
        "requirePermissionToInitiateTransactions": false,
        "requirePermissionToInitiateTransactionsOptions": {
          "callToAction": {
            "type": "none"
          }
        },
        "requirePermissionToRead": false,
        "requirePermissionToReadOptions": {
          "callToAction": {
            "type": "none"
          }
        }
      }
    }
  }
}
```

In addition to permission data, the asset contains an options object, which contains data on a Call To Action button. 

## Permissions in the currentUser resource
Permissions show up in the `currentUser` resource in two ways:
1. `currentUser` has an attribute `permissions`, which contains the user-level permission setting.
2. `currentUser` also has a related resource `effectivePermissionSet`, which contains the user's permissions based on the user level and marketplace level settings. 

You will need to explicitly [include this related resource](https://www.sharetribe.com/api-reference/#including-related-resources) in your `currentUser.show()` API call to fetch it from the API.

This is an important distinction since the `currentUser.attributes.permissions` value might be different from the `effectivePermissionSet` value.

### Conclusion
Understanding the various user access controls allows you to tailor the user experience on your marketplace effectively. For any additional support, reach out to the Sharetribe support team or consult the developer community.
```