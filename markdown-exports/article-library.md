```markdown
---
title: "Create a library of articles"
source: "Sharetribe Developer Platform documentation"
extracted_date: "2023-10-24"
summary: "With the Pages feature, you can build content pages on your marketplace directly from Sharetribe Console. This how-to guide shows how to create a blog-style library of interlinked articles on your marketplace."
---

- [Why an article library?](#why-an-article-library)
- [Decide your article content](#decide-your-article-content)
- [Build your library page and article pages](#build-your-library-page-and-article-pages)
  - [Build your library page](#build-your-library-page)
  - [Build your article pages](#build-your-article-pages)
- [Link to other pages](#link-to-other-pages)

# Create a library of articles

With the Pages feature, you can build content pages on your marketplace directly from Sharetribe Console. This how-to guide shows how to create a blog-style library of interlinked articles on your marketplace.

## Why an article library?

An article library functions as a channel for [content marketing](https://www.sharetribe.com/academy/how-to-do-local-content-marketing-for-marketplaces/). You can educate your marketplace users and provide value, [build a community around your marketplace](https://www.sharetribe.com/academy/turn-marketplace-community/), as well as improve your [marketplace SEO ranking](https://www.sharetribe.com/academy/marketplace-seo/opportunities-and-challenges/).

If you want more blog-like features on your article library, e.g. comments, or articles displayed in a reverse chronological order, you can add them with custom development.

## Decide your article content

The first step in building an article library is to [decide on your content strategy](https://www.sharetribe.com/academy/marketplace-business-slows-down/#double-down-on-content), and create an initial list of articles you will feature on your marketplace. For instance, a cottage rental marketplace could have articles on:
- The history of cottages in the marketplace location
- Types of cottages: level of amenities such as electricity and running water, and considerations related to each for guests and hosts
- Reasons to rent out your cottage to others
- Reasons to hire a cottage for your vacation
- Cottage rental etiquette
- What to pack for a cottage trip
- Cottage cooking favorites, such as grilled sausages and fireside s'mores

For the best reader and SEO experience, you want to have articles that can be linked to at least two or three other articles within your article base. You can start with e.g. half a dozen article topics, and then build your content library as you go.

## Build your library page and article pages

To add a page, you need to navigate to [Sharetribe Console > Build > Content > Pages](https://console.sharetribe.com/content/pages). By default, this page contains an about page, a landing page, a privacy policy page, and a terms of service page. Under those pages you can see a link with text "+ Create new page...". Click this link.

![Page creation modal](https://docs/sharetribe/8e8fe2ee035a1b6ce6354a1b1627d58d/e3b24/create-page-modal.png)

### Build your library page

As the page ID for this first page, enter "articles". This will be the collection page where you will link all your individual articles, and you can see the page in *[your-marketplace-url.com]/p/articles*.

After you create the page, you can start adding new sections. Let's add the following sections to this main page:
- Introduction section with *article* template. You can use this section to explain more about the focus of your marketplace and your article collection.
- Featured articles with *features* template. You can use this section to highlight your most important content, best performing articles, or an interesting series of posts.
- List of all articles with *carousel* template. You can use this section to list all your articles, either ordered chronologically or in some other way you prefer.

![Articles sections](https://docs/sharetribe/931c4c99c81f0462543aac98676cfb81/a0279/articles-sections.png)

In each section, you can define a title, ingress content, and blocks for your copy text. After you save the changes you made, you can click the "View page" link in the top right corner of the page editor to see your changes.

### Build your article pages

You have now created your main article collection page. Next, you will create a few article pages with your actual article content. If you were creating a marketplace about cottages, you could first write articles with the following page IDs:
- history
- packing-list
- amenities

You can create these article pages in a similar manner to how you created the collection page. Depending on the design of your pages, you may want to use *article* sections for the main part of the text, and e.g. *features* sections to highlight quotes or other key information.

<div class="banner">
  <h6>Information</h6>
  <p>Sharetribe currently has a limit of <strong>1000 content pages</strong> across your marketplace, including the pages included by default:</p>
  <ul>
    <li>About</li>
    <li>Landing page</li>
    <li>Privacy policy</li>
    <li>Terms of service</li>
  </ul>
</div>

## Link to other pages

Once your articles have been created, you need to link the articles to each other. For instance, in your article on *amenities*, you could link to the other two articles in the following way:

```
...
For a wood-heated cottage, it makes sense to bring your woollen socks – even in summer,
the cottage may be cool inside, and [having the right gear](/p/packing-list) reduces
the need for heating during the cooler months.

For [older cottages](/p/history), amenities will likely be more modest.
...
```

The example above uses the [Markdown](https://www.markdownguide.org/getting-started/) style of creating links:
- The link text is wrapped in [square brackets]
- The link address is added immediately after the closing bracket in (curved brackets).

In addition to adding links in the text in Markdown, you can add calls to action in both sections and blocks. A call to action can be either an internal link or an external one.

![Adding a call to action](https://docs/sharetribe/fc0bdcef562ad77fc783347a22b1d622/e60a8/call-to-action-UI.png)

---

### Can't find what you're looking for?
Reach out to our support team by [email](mailto:hello@sharetribe.com), or consult the Sharetribe Developer Community at [Sharetribe Developers Slack Channel](https://www.sharetribe.com/dev-slack). You can also contact our support team through the chat widget in Console.

```