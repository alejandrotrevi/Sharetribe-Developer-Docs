```markdown
---
title: "Rate Limiting in Marketplace API and Integration API"
source: "provided"
extracted_date: "2023-10-24"
summary: "This article describes rate limiting in Marketplace API and Integration API, as well as suggests how to handle those limits gracefully."
---

- [Table of Contents](#table-of-contents)
- [Rate Limits Overview](#rate-limits-overview)
- [Interaction Between Different Rate Limits](#interaction-between-different-rate-limits)
- [Handling Rate Limits in Marketplace API](#handling-rate-limits-in-marketplace-api)
- [Handling Rate and Concurrency Limits in Integration API](#handling-rate-and-concurrency-limits-in-integration-api)
  - [Integration SDK](#integration-sdk)
  - [Integration API](#integration-api)

# Rate Limits Overview
Starting in January 2023, Marketplace API and Integration API feature new rate limits in Dev and Test environments. In addition, Integration API features new concurrency limits in Dev and Test environments and, eventually, in Live environments.

## What are Rate Limits and Concurrency Limits?
- **Rate limit**: A limit on the number of requests that the API can process within a time span, e.g., in one minute.
- **Concurrency limit**: A limit on the number of simultaneous requests that the API can process at any given moment.

The rate limits are different for queries (fetching data) and commands (modifying data):
- Queries are rate limited at 1 request per second (60 requests per minute) on average.
- Commands are rate limited at 1 request per 2 seconds (30 requests per minute) on average.
- The rate limit applies per client IP address. More information can be found in the [Marketplace API](https://www.sharetribe.com/api-reference/marketplace.html#rate-limits) and [Integration API](https://www.sharetribe.com/api-reference/integration.html#rate-limits).

**Note**: Live environments are currently not rate limited, except for one endpoint in Integration API. It is appreciated if you also transfer rate and concurrency handling into production.

# Interaction Between Different Rate Limits
All query and command endpoints have rate limits in Dev and Test environments. In addition, the Integration API listing creation endpoint has a separate rate limit:

- If the command rate limit burst capacity has not been depleted, listing creation is rate limited at 100 API calls per minute, which depletes the command rate limit accordingly.
- If the command rate limit burst capacity is depleted, listing creation is rate limited at the regular command rate limit of 30 requests per minute.
- Other commands occurring while listings are created will count towards the command rate limit.

# Handling Rate Limits in Marketplace API
In standard usage, the rate limit will largely be undetectable. However, if your Test environment is exposed to the public web, a traffic spike from bots or crawlers may result in rate-limiting, preventing server-side rendering. We strongly recommend using [basic HTTP authentication](https://www.sharetribe.com/docs/tutorial/deploy-to-render/#enable-http-basic-access-authentication) on deployed test marketplace applications.

# Handling Rate and Concurrency Limits in Integration API
Rate limits will likely be more visible when developing with the Integration API, which also features concurrency limiting.

### Integration SDK
The Sharetribe Integration SDK is configured to handle concurrency limits by default starting from version 1.9.0. You can pass configurations for query and command rate limiters. For details, see the [SDK documentation](https://sharetribe.github.io/flex-integration-sdk-js/rate-limits.html).

For listing creation rate limits, you will need to implement your own handling logic. We have an example in our [Integration API example scripts repository](https://github.com/sharetribe/integration-api-examples/blob/master/scripts/create-listings.js).

**Important Notes**: 
- Rate limits apply by client IP address. If you have multiple SDK instances running on the same server, they may collectively exceed the limits.
- Customize rate limits to lower values, customize `httpsAgent` to use `maxSockets` set lower than 10, implement a retry with random exponential backoff if receiving a 429 response, or a combination of these.

### Integration API
If you are not using the Integration SDK, we recommend handling your own rate and concurrency limits, using libraries like [bottleneck](https://www.npmjs.com/package/bottleneck) for Node.js or similar.

---

**Can't find what you're looking for?**  
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the [Sharetribe Developer Community](https://www.sharetribe.com/dev-slack).
```