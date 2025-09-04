```markdown
---
title: "Extended Data"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2025-04-14"
summary: "Reference documentation providing information on all the different types of extended data."
---

- [Overview](#overview)
- [Table of Contents](#table-of-contents)
- [Types of Extended Data](#types-of-extended-data)
  - [Public Data](#public-data)
  - [Protected Data](#protected-data)
  - [Private Data](#private-data)
  - [Metadata](#metadata)
- [Search Schema](#search-schema)

# Overview
Extended data is a set of arbitrary keys and values stored with the API resources. The values for the keys can be any valid JSON values, including a JSON object (hash). This provides API clients with the capability to store arbitrary structured data for the supported resource types. Via search schema, we also support querying, filtering, and sorting by extended data for some value types.

For all types of extended data across all resources, the total size of an extended data object as a JSON string must not exceed 50KB.

## Table of Contents
1. [Types of Extended Data](#types-of-extended-data)
2. [Public Data](#public-data)
3. [Protected Data](#protected-data)
4. [Private Data](#private-data)
5. [Metadata](#metadata)
6. [Search Schema](#search-schema)

## Types of Extended Data
**Note:** Extended data support in API resources is work in progress. See the [API reference](https://www.sharetribe.com/docs/concepts/api/) for each resource for information on supported extended data.

There are four types of extended data: *metadata*, *private data*, *protected data*, and *public data*. Each type has different access semantics (i.e., who is allowed to read or write the data).

### Public Data
*Public data* is writable by whoever has write access to a given resource (e.g., the listing's author can write public data about the listing, a user can write public data in their profile). Public data is readable via all API endpoints that return the resource and by marketplace operators.

Public data can be used, for instance, to store additional information about listings and users in order to help customers make buying decisions.

### Protected Data
*Protected data* is writable and readable by whoever has write access to a resource. It is also readable by marketplace operators. Protected data can be revealed to all transaction parties via the marketplace [transaction process](https://www.sharetribe.com/docs/concepts/transaction-process/).

Protected data can be used to store information that is only made visible to the transaction participants at a specific point in the [transaction process](https://www.sharetribe.com/docs/concepts/transaction-process/).

### Private Data
*Private data* is similar to protected data but is not intended to be revealed via the transaction process. For instance, it can be used to collect and store information about users or listings for marketplace operators.

### Metadata
*Metadata* is writable by marketplace operators and can be read via all API endpoints returning the corresponding resource. Metadata is supported for users and listings. It can also be used as filters when searching via 
[ /listings/query in Marketplace API](https://www.sharetribe.com/api-reference/marketplace.html#query-listings),
[ /listings/query in Integration API](https://www.sharetribe.com/api-reference/integration.html#query-listings), or
[ /users/query in Integration API](https://www.sharetribe.com/api-reference/integration.html#query-users).

Metadata can be used to store data about listings and users that must only be writable by marketplace operators or through integrations to other systems built around the Integration API and that the users themselves must not be able to modify.

## Search Schema
Extended data is available out of the box and can be written and read via the Marketplace API, Integration API, and Console without any prior configuration. *Search schema* may optionally be defined for some value types of extended data. When a schema is provided for a given extended data key, the API can use this information to make querying the extended data possible via some API endpoints. For instance, 
[ /listings/query](https://www.sharetribe.com/api-reference/marketplace.html#query-listings) supports querying listings by public data or metadata.

Search schemas can be managed with [Sharetribe CLI](https://www.sharetribe.com/docs/introduction/getting-started-with-sharetribe-cli/).

With the CLI, you can list, set, and unset search schemas for the following kinds of extended data:

- Listing's public data and metadata
- User profile's metadata, private, protected, and public data
- Transaction's protected data and metadata

The commands to manage search schemas are:
- `flex-cli search` - List all defined data schemas
- `flex-cli search set` - Set (create or update) data schema
- `flex-cli search unset` - Unset data schema

You can also check out our guide for [managing search schemas with Sharetribe CLI](https://www.sharetribe.com/docs/how-to/manage-search-schemas-with-sharetribe-cli/).

---

Only top-level values in extended data can have a schema.
```