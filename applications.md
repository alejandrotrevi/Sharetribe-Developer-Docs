```markdown
---
title: "Applications"
source: "provided"
extracted_date: "2023-10-24"
summary: "Sharetribe Applications are the entities that authenticate to and access the Sharetribe APIs."
---

- [Introduction](#introduction)
- [Best Practices](#best-practices)

# Introduction
Sharetribe Applications are the entities that authenticate to and access the Sharetribe APIs.

In Sharetribe, accessing the [Marketplace API or the Integration API](https://www.sharetribe.com/docs/concepts/marketplace-api-integration-api/) starts with creating an [*application*](https://console.sharetribe.com/advanced/applications). Each application has the necessary credentials (client ID and optional client secret) that are used to [authenticate](https://www.sharetribe.com/docs/concepts/authentication-api/) your application to the Sharetribe APIs.

Applications come in two types: Marketplace API applications and Integration API applications. Both types of applications have both a client ID and a client secret. As a general rule, the client ID can be considered public information, while the client secret must always be kept secure.

## Best Practices
- Create a separate application for each of your systems that access the Sharetribe APIs. For example, if you have a web and a mobile app, create two separate Marketplace API applications.
- Name your applications so that it is easier for you to recognize how each one is used. For instance, use names like "Web UI", "iOS mobile app", "reporting", "mailing list integration", etc.

<div class="Banner__BannerContainer-sc-7v4f78-0 hpWVVa">
  <div>
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" bgcolor="#FFC3C3" class="Warning__Icon-sc-1piyju6-0 dDyeSr">
      <title>Warning</title>
      <path d="M85.57 446.25h340.86a32 32 0 0028.17-47.17L284.18 82.58c-12.09-22.44-44.27-22.44-56.36 0L57.4 399.08a32 32 0 0028.17 47.17z" fill="currentColor"></path>
      <path d="M250.26 195.39l5.74 122 5.73-121.95a5.74 5.74 0 00-5.79-6h0a5.74 5.74 0 00-5.68 5.95z" fill="#FFC3C3" stroke="#FFC3C3" stroke-linecap="round" stroke-linejoin="round" stroke-width="32"></path>
      <path d="M256 397.25a20 20 0 1120-20 20 20 0 01-20 20z" stroke="#FFC3C3" fill="#FFC3C3"></path>
    </svg>
    <h6 class="Banner__Title-sc-7v4f78-1 kmzNxg">Warning</h6>
  </div>
  <div class="Banner__BannerContent-sc-7v4f78-2 inAPHP">
    <p>Always keep your applications' client secret secure. Never expose it in an untrusted device or application, such as end users' browsers or mobile apps.</p>
  </div>
</div>

```