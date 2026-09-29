---
name: new-post
description: Create or revise a post in content/blog/ — filename, frontmatter, tags, social card, intro structure. Use when writing a blog post or updating an existing post's frontmatter or `updated` field.
---

# New blog post

Creates a post matching the conventions of the existing ones. Read
`content/blog/2025-06-14_fjord-cruise-oslo.md` as the reference — it uses the
full range of the site's shortcodes. Field rules are in `AGENTS.md`; this is
the workflow.

## 1. Settle the basics

Ask only about details that would change the post.

- **Date** — when the post is about, not when it was written. This is both the
  filename prefix and the `date` field. Use today for a new event write-up.
- **Slug** — short, kebab-case, no date: `2025-06-14_fjord-cruise-oslo.md`.
- **Title** — sentence case, no trailing punctuation.
- **Description** — one or two sentences that stand alone without the body.
  This is what appears in the feed and every social preview, so it decides
  whether anyone clicks.

## 2. Create the file

```sh
content/blog/YYYY-MM-DD_slug.md
```

```toml
+++
title = "Fjord Cruise Oslofjord"
description = "Accessible and silent fjord cruise in Oslo with the electric boat The Fjord — a beautiful experience, but a bit pricey."
date = "2025-06-14"

[extra]
social_media_card = "/img/events/fjord-cruise.webp"

[taxonomies]
tags = ["norway", "oslo", "trip", "fjord", "cruise"]
+++

Opening paragraph goes directly below the frontmatter, no heading.
```

Three things the shape above doesn't show:

- `updated` appears only when revising an existing post, never on a new one.
- Omit the `[extra]` block entirely rather than leaving it empty when there is
  no local image for the card.
- Reuse tags that already exist before inventing new ones:

```sh
grep -rh '^\[taxonomies\]' -A1 content/blog/*.md | grep 'tags ='
```

## 3. Write the body

- Lead with prose — that first paragraph is what shows in listings and feeds.
- Use `##` for sections. Two to four reads well; none is fine for a short post.
- Link to sources you actually used, and close with recommendations when
  reviewing a place or product.
- Tables and fenced code blocks are styled by the theme — use them for recipes
  and itineraries.
- Link existing tags inline (`[Oslo](/tags/oslo)`) to connect related posts.

## 4. Add media

Use the `blog-media` skill rather than hand-writing image tags. It covers the
shortcodes, SmugMug size selection, and converting local images to WebP.

## 5. Verify

```sh
mise run verify
mise run start   # read the post in the browser
```

Check the rendered page, not the source: the social card resolves, no image is
oversized, and the intro works without the title above it.

## Drafts

`draft = true` in the frontmatter keeps a post out of the build entirely — it
will not appear in listings, feeds, the sitemap, or search. Remove it when
publishing.
