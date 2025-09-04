```markdown
---
title: "API Reference"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2023-10-24"
summary: "Introduction to the Sharetribe Marketplace API reference documentation."
---

- [Introduction](#introduction)
- [Postman collection](#postman-collection)

# API Reference
**Introduction to the Sharetribe Marketplace API reference documentation.**

## Introduction
**[Access the Sharetribe API reference here](https://www.sharetribe.com/api-reference/)**

The Sharetribe API consists of the following individual APIs:
- [Marketplace API](https://www.sharetribe.com/api-reference/marketplace.html)
- [Integration API](https://www.sharetribe.com/api-reference/integration.html)
- [Authentication API](https://www.sharetribe.com/api-reference/authentication.html)
- [Asset Delivery API](https://www.sharetribe.com/api-reference/asset-delivery-api.html)

All these are HTTP APIs and both the Integration API and Marketplace API support [JSON](https://www.ietf.org/rfc/rfc7159.txt) and [Transit](https://github.com/cognitect/transit-format) formats. The Authentication API is based on OAuth2. Our API documentation provides further technical detail on all four APIs. If you are looking for a way to use the APIs in your client or server application, refer to our documentation on the [SDK for JavaScript](https://sharetribe.github.io/flex-sdk-js/) or the [Integration SDK for JavaScript](https://sharetribe.github.io/flex-integration-sdk-js/).

If you are interested in reading more about the Marketplace and Integration API, refer to our article on [Marketplace and Integration API](https://sharetribe.com/docs/concepts/marketplace-api-integration-api/). For more information on our Authentication API, refer to our article on the [Authentication API](https://sharetribe.com/docs/concepts/authentication-api/). For more information on the Asset Delivery API, refer to the [asset reference](https://sharetribe.com/docs/references/assets/) article.

## Postman collection
You can also explore and test our API endpoints with our [Postman Collection](https://www.postman.com/sharetribe-api/workspace/sharetribe-public-workspace/collection/19129702-bd56ce4d-6877-4664-87a0-2cfdeed9db4d). In this collection you can find all available API endpoints. The collection uses pre-request scripts to authenticate requests. Bearer tokens are stored in collection variables. Therefore, you don't have to worry about authentication requests in this collection after you store client credentials in the collection variables.

To start using the collection, create a Postman account and fork the collection. After that, add your API credentials to the Collection variables via the variables tab in Postman. Remember not to persist any credentials stored in variables if you're publicly sharing your forked collection.

Click here to fork the Postman collection into your workspace:
[![Run In Postman](https://run.pstmn.io/button.svg)](https://god.gw.postman.com/run-collection/19129702-bd56ce4d-6877-4664-87a0-2cfdeed9db4d?action=collection%2Ffork&source=rip_markdown&collection-url=entityId%3D19129702-bd56ce4d-6877-4664-87a0-2cfdeed9db4d%26entityType%3Dcollection%26workspaceId%3D6c2b02d7-1f11-4f86-b110-6af5845eb59c)

## Additional Information
### Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.
```