---
title: 'Coding: Google Search Console reports duplicate field url'
date: 2025-07-13 12:24:05
tags:
    -- SEO 
    -- Google Search Console
    -- pnl.dev 
    -- bugfix
---
Google Search Console reports a critical issue ⚠️ for [pnl.dev](https://pnl.dev): a 'duplicate URL property' is preventing my website from being indexed 🚫.

It turns out the problem was caused by a small HTML attribute: `itemprop="url"` 🧩. This attribute is part of [Microdata](https://developer.mozilla.org/en-US/docs/Web/HTML/Microdata), used to define metadata for search engines. But if misused, it can confuse crawlers and hurt indexing 📉.

![screenshot of the issue on google search console](/img/post/seo/duplicate-field-url.png)

## What is `itemprop="url"`?

The `itemprop` attribute is used within [Schema.org](https://schema.org/) microdata to specify properties of an item. For example, when marking up a website's organization or a person, you might use:

```html
<a href="https://example.com" itemprop="url">Visit this Site</a>
```

This tells search engines that the link is the canonical URL for the entity.

## The Problem with Duplicate URLs

If multiple elements on a page use `itemprop="url"` with the same or conflicting values, search engines may get confused about which URL is the "official" one. This can lead to:

- **Indexing issues**: Google may skip or incorrectly index your page.
- **Rich result errors**: Structured data may not be eligible for enhancements in search results.
- **Manual actions**: In rare cases, repeated misuse can trigger manual penalties.

## How I Diagnosed the Issue

1. **Google Search Console**: The error message pointed to a duplicate URL property.
2. **HTML Inspection**: I searched for all instances of `itemprop="url"` in my templates.
3. **Validation Tools**: I used the [Rich Results Test](https://search.google.com/test/rich-results) and [Schema Markup Validator](https://validator.schema.org/) to confirm the problem.

## Solution

- **Remove unnecessary `itemprop="url"` attributes** from elements that don't represent the canonical URL.
- **Ensure only one canonical URL per entity** is marked up with `itemprop="url"`.
- **Test again** with Google's tools to verify the fix.

## Key Takeaways

- Validate your structured data after making template changes.
- Use `itemprop="url"` only where it makes sense semantically.
- Duplicate or conflicting microdata can harm your SEO.

**Lesson learned:** Even small HTML attributes can have a big impact on how search engines see your site. Keep an eye on your Google Search Console, and always validate your structured data after changes to avoid unexpected SEO issues.