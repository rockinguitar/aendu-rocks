---
name: proofread
description: Proofread a post or page in content/ — typos, grammar, and spelling, normalised to the site's British-leaning English without flattening the author's voice. Use when asked to proofread, fix typos or grammar, or copy-edit a post.
---

# Proofreading

Fixes mechanics, not style. The aim is a post with no typos and consistent
house English, still unmistakably written by André.

## Fix these

- Typos, doubled or dropped words, missing spaces after punctuation
- Subject–verb agreement, article errors, wrong prepositions
- Comma splices and run-on sentences that genuinely impede reading
- Inconsistent capitalisation of a term you deliberately capitalise

## British-leaning English

The house style is British English with room for the occasional neutral or
proper-noun spelling. Prefer `-ise` over `-ize`, `-our` over `-or`, `-re` over
`-er` for words like `centre` and `traveller`, and `travelling` with two l's.

The Americanisms in the content, all of which a pass should fix:

| American | British | Where |
|---|---|---|
| `favorite` | `favourite` | 5 files, 8 occurrences |
| `color` | `colour` | `sables.md` |
| `colorful` | `colourful` | `lindesnes.md` |
| `harbor` | `harbour` | `lindesnes.md` |
| `traveling` | `travelling` | `who-i-am.md` |
| `traveler` | `traveller` | `who-i-am.md` |

These look American but are correct — do not "fix" them:

| Word | Why it stays |
|---|---|
| `Dream Theater` | Band name |
| `practice` (in `lindesnes.md`) | A noun, and correct in British English. Only the *verb* is `practise` |
| `centered-text` | A CSS class in a Markdown attribute, not prose |
| `Sablé`, `Lindesnes` | French and Norwegian words |

A blanket find-and-replace for `-er` endings will break the first three.

## Leave these alone

- **The author's voice.** This is a personal blog with strong opinions and
  deliberate repetition for effect. Fix the mechanics, keep the character.
  Do not make a sentence more formal, shorter, or "better" than it is.
- **Jokes.** `voice-messages-are-a-sin.webp` and the surrounding rant are the
  post's point. Straighten the grammar, keep the joke.
- **Intentional informality.** Sentence fragments for rhythm, single-word
  paragraphs, rhetorical questions.
- **Markdown-significant whitespace.** Two trailing spaces are a hard line
  break — the cocktail recipes in `cosmopolitan.md` depend on them. A
  trailing-whitespace cleanup will silently break those lists.
- **Straight quotes.** `smart_punctuation = true` in `config.toml` makes Zola
  convert them at build time. Do not pre-empt it.
- **Frontmatter**, unless asked. Do not change `title`, `date`, or `tags` while
  proofreading prose.

## Punctuation

Use spaced em-dashes — `word — word` — as the rest of the site does.
`cosmopolitan.md` is the outlier, with three closed dashes (`cocktail—often`,
lines 11, 19, and 23); normalise them.

## Process

1. Read the whole post first. Proofreading line by line misses repetition
   across sections.
2. Fix what you are sure of. Where a sentence is ambiguous, leave it and
   raise it — do not guess at intent.
3. Show the changes as a list of before/after pairs, grouped by file. Do not
   silently rewrite a paragraph; the author decides what lands.
4. Flag anything you deliberately left, with the reason.

## After editing

```sh
mise run verify
```

Then re-read in the browser (`mise run start`) — a "fix" that changes how a
list renders is not a fix.
