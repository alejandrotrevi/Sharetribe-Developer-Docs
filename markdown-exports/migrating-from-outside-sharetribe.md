---
title: "Migrating from outside Sharetribe ecosystem"
source: "provided"
extracted_date: "2024-01-15"
summary: "How to import data from outside Sharetribe ecosystem"
---

- [When to request a migration?](#when-to-request-a-migration)
- [Intermediary data](#intermediary-data)
  - [Format](#format)
  - [Supported types](#supported-types)
  - [Value types](#value-types)
- [Things to consider](#things-to-consider)
  - [No updates are supported](#no-updates-are-supported)
  - [Test import](#test-import)
  - [Password management](#password-management)
  - [Security and sharing data](#security-and-sharing-data)
- [A full example of Intermediary format](#a-full-example-of-intermediary-format)

# Migrating from outside Sharetribe ecosystem

How to import data from outside Sharetribe ecosystem

Data from an existing marketplace can be migrated to Sharetribe. If your marketplace is running in Sharetribe Go, there is a ready migration path that will be handled by us. We will handle the migration in this case and you can find the outline for this process [here](https://www.sharetribe.com/help/en/articles/8418385-migrating-from-sharetribe-go-to-the-new-sharetribe).

If you are running a marketplace outside the Sharetribe ecosystem, and would like to import data to Sharetribe, it is possible. You will need to extract your data from its existing storage and transform it into Sharetribe's proprietary data format called Intermediary. Sharetribe will then validate the transformed data and load it into Sharetribe.

This article will outline how you can encode data into Intermediary format, give you syntax examples of the format, and provide instructions on the migration process.

## When to request a migration?

You should request a migration when:

- You have a marketplace operating outside the Sharetribe ecosystem
- You know you want to transfer your marketplace users and listings to your Sharetribe marketplace. If you'd prefer to re-start your community in Sharetribe, then a migration is not needed.
- You have started developing with Sharetribe or configured your no-code settings to get started with a no-code Sharetribe marketplace.

You will work with Sharetribe's engineers to complete your migration. If you're planning to migrate your data to Sharetribe, you should always start the process with a test migration to your Dev or Test environment, and ensure everything looks correct there, before doing a live migration. If you want to initiate the test migration process, you should email [hello@sharetribe.com](mailto:hello@sharetribe.com) with the subject "Migrating from outside Sharetribe". Please include your Sharetribe marketplace ID from your [Console](https://console.sharetribe.com/) > Build > General > Marketplace name.

## Intermediary data

Intermediary is an edn ([https://github.com/edn-format/edn](https://github.com/edn-format/edn)) data format that allows defining data rows for marketplaces. It supports creating links between rows via references.

We recommend that you use an edn library to generate the data and validate the .edn syntax to ensure that the file is up to standard.

> **How to generate edn using a library?**
>
> edn libraries exist for multiple languages, for example [JavaSript](https://www.npmjs.com/search?q=edn), [Python](https://pypi.org/search/?q=edn), and [Ruby](https://rubygems.org/search?query=edn). Clojure has built-in support for edn. Usually these libraries support encoding data structures to edn and creating custom tagged elements.
>
> Here's an example of how to write a bit of intermediary data in edn format using JavaScript libraries [jsedn](https://www.npmjs.com/package/jsedn) and [uuid](https://www.npmjs.com/package/uuid) to represent a user and a listing:
>
> ```javascript
> const edn = require('jsedn');
> const { v4: uuidv4 } = require('uuid');
> 
> const tagged = (tag, value) => new edn.Tagged(new edn.Tag(tag), value);
> const uuid = () => tagged("uuid", uuidv4());
> const price = (amount, currency) => tagged("im/money", [amount, currency]);
> 
> const email = data => {
>   const { emailAddress } = data;
>   return new edn.Map([edn.kw(":im.email/address"), emailAddress,
>                       edn.kw(":im.email/verified"), true]);
> };
> 
> const profile = data => {
>   const { firstName, lastName } = data;
>   return new edn.Map([edn.kw(":im.userProfile/firstName"), firstName,
>                       edn.kw(":im.userProfile/lastName"), lastName]);
> };
> 
> const user = data => {
>   const { alias, emailAddress, firstName, lastName } = data;
>   const role = new edn.Vector([edn.kw(":user.role/customer"), edn.kw(":user.role/provider")]);
> 
>   return new edn.Vector([new edn.Vector([edn.kw(":im.user/id"), uuid(), alias]),
>                          new edn.Map([
>                            edn.kw(":im.user/primaryEmail"), email(data),
>                            edn.kw(":im.user/createdAt"), tagged("inst", new Date().toISOString()),
>                            edn.kw(":im.user/role"), role,
>                            edn.kw(":im.user/profile"), profile(data)
>                          ])
>                         ]);
> };
> ```

### Format

Intermediary is specified as [a map](https://clojure.org/reference/data_structures#Maps) with the following keys:

- :ident - identifies the target marketplace by marketplace ID,
- :data - a [seq](https://clojure.org/reference/sequences) of marketplace data rows. Each row is a 2-tuple (an array with two elements) of id and content.

You can find your marketplace ID in Sharetribe Console > Build > General. Note that the anonymised test file needs to specify your dev environment marketplace ID and your live data file needs to specify your live environment marketplace ID.

The id part of the data row 2-tuple is specified as a tuple of 1 to 3 elements. The first element is always an id attribute and identifies the row type. The second element can be an alias or an import id. A 3 element version has id attr, import id and an alias.

```text
;; 1 element tuple
[:im.stripeAccount/id]

;; 2 element tuple with import id
[:im.image/id #uuid "58afd8e1-e336-4ca4-a1e7-ff1d91856a6c"]

;; 2 element tuple with alias
[:im.user/id :user/jane]

;; 3 element tuple
[:im.listing/id #uuid "b074e697-ab0c-4746-a195-c58d73606b1f" :listing/rock-sauna]
```

Import ids can be given to rows and they should be unique for the marketplace and have type [UUID](#uuid). Aliases can be used to reference rows from other rows. Aliases are useful when the data is written by hand. Import ids are useful for programmatically generated / exported data.

Note that import ids are only used in the import phase and they can not be mapped to existing resource ids, because the final resource id is not created based on the import id. In other words, if e.g. your dev marketplace already has data rows, you cannot reference those rows by import ids in the Intermediary file.

If you are not generating data by hand, it is recommended that you use the 2 element form of the id tuple. This means using unique UUIDs throughout the file to reference entities. You can read more about [generating UUIDs for your data here](https://www.uuidgenerator.net/dev-corner).

#### Aliases

If you currently use non-UUID ids in your data, you can also use them to generate aliases for the data. Aliases must be unique across the file, and they cannot contain spaces.

Since aliases are namesp