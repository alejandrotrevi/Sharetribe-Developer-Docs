```markdown
---
title: "Using Console and the API to manage Pages"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2025-04-24"
summary: "This article explains how data can be edited through Console and how it can be queried using the Asset Delivery API."
---

- [How is Pages "headless"?](#how-is-pages-headless)
- [Querying the Asset Delivery API](#querying-the-asset-delivery-api)

# Using Console and the API to manage Pages

This article explains how data can be edited through Console and how it can be queried using the Asset Delivery API.

## How is Pages "headless"?

The Pages feature allows content editors to make changes to content pages without having to touch code. Pages is a “headless” feature, where the content is decoupled from the frontend and can be accessed via API.

In Sharetribe, the presentational layer, or frontend, is usually the Sharetribe Web Template (if you are not using a custom client application), and the content is the data that is managed through Pages. The headless architecture allows you to render the content in any client you choose, as it can be retrieved by a call to the Sharetribe API.

To illustrate further, here is an example of how data fetched from the API maps to visual elements in the default “About page” in the template:

![How data is rendered on the about page](https://docs/static/283de3207a451ea7a56fc80ca03be3e4/88689/data.png)

## Querying the Asset Delivery API

The content you edit through Console can be queried through the Asset Delivery API. The template queries this data automatically and renders the content on dynamic content pages using it. Learn more about how the template renders content pages [here](https://sharetribe.com/docs/template/page-builder/).

A basic query to the Asset Delivery API to fetch the content of the landing page looks like this:

```bash
curl https://cdn.st-api.com/v1/assets/pub/client_id/content/pages/landing-page.json
```
Remember to replace `client_id` with your client ID.

Through the Sharetribe SDK, this query is formatted as:

```javascript
sdk.assetByAlias({
  path: 'content/pages/landing-page.json',
  alias: 'latest',
});
```
You can try this out in the [SDK playground](https://sharetribe.github.io/flex-sdk-js/try-it-in-the-playground.html), curl, or by pasting the Asset Delivery API query into your web browser's address bar. If you fetch the data through your browser, you should see something like this:

![Example data](https://docs/static/62305ba2e2e296fabf05619ed177be57/6c116/example-data.png)

This is the raw JSON API response that encodes the content and structure you have entered into Console. Try pasting the response into your preferred code editor to make it easier to interpret:

![Example data formatted](https://docs/static/ee2ceffd45bcff56be6a363c5eac7684/c4c49/formatted.png)

This data is meant for your client application to interpret. Sharetribe Web Template can automatically render a landing page using this data.

### Information

Read more about the structure of page asset data:
- [Page asset schema reference](https://sharetribe.com/docs/references/page-asset-schema/)

To see how changes in the Console are reflected in the actual data, let’s make a change to the landing page through Console. Edit the title value through the Page Editor:

![Changing a value through Console](https://docs/static/fa533c17ba91a9c0d3ba01d12738fcd2/090d4/console-change.png)

Fetch the most recent data from the Asset Delivery API either through the SDK or through your browser. You’ll see that the change we made in Console is now visible in the JSON data:

```json
{
  "data": {
    "sections": [
      {
        "title": {
          "content": "Test title"
        }
      }
    ]
  }
}
```

## Can't find what you're looking for?

Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.

---
```