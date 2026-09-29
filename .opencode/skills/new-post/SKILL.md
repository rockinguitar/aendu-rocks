---
name: new-post
description: Scaffold a new post in content/blog/ with correct filename, frontmatter, tags, social card, and intro structure. Use when the user wants to write, start, or add a new blog post or article.
---

# New blog post

Creates a post in `content/blog/` that matches the conventions of the existing
ones. Read `content/blog/2025-06-14_fjord-cruise-oslo.md` as the reference
example — it uses the full range of the site's components.

## 1. Confirm the basics

Settle these before writing anything. Ask only if a detail would actually
change the post.

- **Date** — the date the post is about, not the day it was written. This is
  the filename prefix and the `date` field. Use today's date for a new event
  write-up.
- **Slug** — short, kebab-case, no date: `2025-06-14_fjord-cruise-oslo.md`.
- **Title** — sentence case, no trailing punctuation.
- **Description** — one or two sentences. It feeds the Atom feed, the sitemap,
  and every social preview, so it must stand alone without the body. Write it
  to be genuinely tempting, not a restatement of the title.

## 2. Create the file

```sh
content/blog/YYYY-MM-DD_slug.md
```

Frontmatter, in this order. Omit `updated` on a new post — it only appears
when an existing post is revised.

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

## 3. Frontmatter checklist

- [ ] `title` — set
- [ ] `description` — set, self-contained, and under ~160 characters so it is
      not truncated in search results and link previews
- [ ] `date` — matches the filename prefix
- [ ] `updated` — omitted unless revising
- [ ] `social_media_card` — set if a local image fits the post; otherwise
      delete the `[extra]` block rather than leaving it empty. The fallback is
      the site avatar, which is wrong for a specific post.
- [ ] `tags` — lowercase, 3 to 6, reusing existing tags where they apply

Check what already exists before inventing tags:

```sh
grep -rh '^\[taxonomies\]' -A1 content/blog/*.md | grep 'tags ='
```

## 4. Write the body

- Lead with prose. The first paragraph is what shows in listings and feeds.
- Use `##` for sections. Two to four reads well; a post with none is fine if
  it is short.
- Link out to sources you actually used, and prefer a closing line of
  recommendations when the post reviews a place or product.
- Markdown tables and fenced code blocks are styled by the theme — use them
  for recipes and itineraries.
- Reuse existing tags as links (`[Oslo](/tags/oslo)`) to build internal
  connections between posts.

## 5. Add media

Delegate images to the `blog-media` skill rather than hand-writing image tags.
It covers the `smugmug`, `floated_image`, and `youtube` shortcodes, SmugMug
size selection, and converting new local images to WebP.

## 6. Verify

```sh
mise run verify
mise run start   # then read the post in the browser
```

Check the rendered page, not just the source. Confirm the social card image
resolves, images are not oversized, and the intro reads without the title
above it.

## Drafts

Set `draft = true` in the frontmatter to keep a post out of the build, the
feeds, and listings. Remove it when publishing. The sitemap and search index
respect drafts, so a drafted post is fully invisible — not just unlisted.
