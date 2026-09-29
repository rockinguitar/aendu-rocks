---
name: proofread
description: Proofread a post or page in content/ — typos, grammar, and spelling, normalised to the site's British-leaning English without flattening the author's voice. Use when asked to proofread, fix typos or grammar, or copy-edit a post.
---

# Proofreading

Fix mechanics, not style. No typos, consistent house English, still
unmistakably written by André.

## Fix

Typos, doubled or dropped words, missing spaces, agreement, articles,
prepositions, capitalisation of terms you deliberately capitalise. Comma
splices only where they genuinely impede reading.

## House English

British-leaning. Prefer `-ise` over `-ize`, `-our` over `-or`, `-re` over
`-er` (`centre`, `traveller`), `travelling` with two l's. Spaced em-dashes —
`word — word` — as most of the site writes them.

| | Word | Where |
|---|---|---|
| fix | `favorite` → `favourite` | 5 files, 8 occurrences |
| fix | `color`, `colorful` | `sables.md`, `lindesnes.md` |
| fix | `harbor` → `harbour` | `lindesnes.md` |
| fix | `traveling`, `traveler` | `who-i-am.md` |
| **leave** | `Dream Theater` | Band name |
| **leave** | `practice` | A noun, correct in British. Only the *verb* is `practise` |

`Sablé` and `Lindesnes` are French and Norwegian. A blanket `-er`
find-and-replace breaks `Theater`, `practice`, and `centered-text`.

## Don't touch

**Alt text** describes the image for a reader who cannot see it. Fix typos,
but never rewrite it to match the prose or count it as a duplicate of it — the
alt and the prose are the same subject with different jobs. It is also HTML, so
`alt="Ingunn &amp; André"` uses a deliberate entity, not a stray `&`.

**URLs and identifiers**: `smugmug` `path` and `thumbnail`, `youtube` `id` and
`playlist`, plus `width`, `height`, `float`. Never spell-correct these. The
`smugmug` `caption` *is* prose — proofread it normally.

**Frontmatter**: `description` has a ~160-character budget and feeds the feed,
sitemap, and social cards, so treat it as standalone copy, not body prose.
Leave `title`, `date`, and `tags` alone unless asked.

**CSS classes** like `{.centered-text}` are not words.

**Markdown whitespace**: two trailing spaces are a hard line break, and the
cocktail recipes in `cosmopolitan.md` depend on them.

**Straight quotes**: `smart_punctuation = true` makes Zola convert them at
build time. Do not pre-empt it.

**Voice and jokes**: a personal blog with strong opinions, deliberate
repetition for effect, sentence fragments, and single-word paragraphs. Fix the
mechanics, keep the character. The `voice-messages-are-a-sin` rant is that
post's point.

## Process

1. Read the whole post first — line by line misses repetition across sections.
2. Fix what you are sure of. Where intent is ambiguous, leave it and raise it
   rather than guessing.
3. Show changes as before/after pairs grouped by file. Do not silently rewrite
   a paragraph; the author decides what lands. Flag what you left, and why.
4. Run `mise run verify`, then re-read in the browser (`mise run start`). A
   fix that changes how a list renders is not a fix.
