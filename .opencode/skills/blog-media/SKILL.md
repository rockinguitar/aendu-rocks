---
name: blog-media
description: Add photos, SmugMug galleries, floated images, and video embeds to posts, and optimise new local images to WebP. Use when adding images or galleries to a post, embedding a SmugMug album, or when an image needs resizing or format conversion.
---

# Images, galleries, and video

## Local or SmugMug?

Almost everything photographic is hosted on SmugMug — use a hot link. Only
commit a local file when the image has no SmugMug equivalent.

| Situation | Approach |
|---|---|
| A trip, event, or album worth revisiting | SmugMug gallery via `smugmug` |
| A photo beside a paragraph of prose | SmugMug via `floated_image` |
| A page illustration, avatar, or social card | Local in `static/img/` |
| A specific photo whose full gallery is irrelevant | SmugMug via plain `![]()` |

## SmugMug size selection

SmugMug encodes the size into the URL path. Always request the size that
matches the display slot — asking for `L` and showing it at 400px wastes
bandwidth on every visitor.

| Slot | Size | Path segment |
|---|---|---|
| Floated beside text | `S` (~400px) | `/S/…-S.jpg` |
| Gallery thumbnail | `M` (~600px) | `/M/…-M.jpg` |
| Full-width figure | `L` (~1024px) | `/L/…-L.jpg` |

The filename must match the segment — SmugMug does not resample on request, so
`/M/…-S.jpg` 404s. Existing posts use `M` most often, then `S`, then `L`.

## Gallery embeds

The standard way to present a photo-heavy trip: floated image to draw the eye
into the opening, then a linked gallery. From
`content/blog/2025-06-14_fjord-cruise-oslo.md`:

```text
{{< floated_image src="https://photos.smugmug.com/photos/i-Mx7qGGv/0/LnBvGKHtN9FSDtBK6vxsqH7hkgcFPsN9qz2NM52nP/S/i-Mx7qGGv-S.jpg" float="right" alt="Ingunn &amp; André on The Fjord" width="400" />}}

Opening prose, two or three paragraphs, no heading.

{{< smugmug path="Events/2025/Fjord-Cruise-Oslo/n-NfGRw4" caption="Click to view the Fjord Cruise Oslo photo &amp; video gallery" thumbnail="https://photos.smugmug.com/photos/i-WtXqHHG/0/NF3bKJ9x9BmWx6jnKTQt4qkcFFhF3m8Z7hxgBGMJP/M/i-WtXqHHG-M.jpg" width="600" height="450" alt="Fjord Cruise Oslo" />}}

The post then continues with its own sections.
```

`path` is the gallery path after `gallery.aendu.rocks`; `thumbnail` is any
photo from that gallery at the right size. Caption it as an invitation —
"Click to view the …" — since the whole point is the click-through.

## Shortcode reference

```text
{{< smugmug path=… thumbnail=… caption=… alt=… width=… height=… />}}
{{< floated_image src=… float="left|right" alt=… width=… height=… />}}
{{< youtube id=… class=… playlist=… autoplay=… />}}
```

- `floated_image` defaults to `float="left"`, `width="300"`. It drops the
  float below 600px, so it is safe to use anywhere in prose.
- `youtube` takes a bare video ID from the URL. It uses
  `youtube-nocookie.com` and adds no JS of its own.
- Write ampersands in shortcode attributes as `&amp;`. It renders as `&`.

Plain markdown images are fine for a sequence of photos in a row, and are
often better than a gallery when the photos *are* the post:

```text
![Friends from Switzerland](https://photos.smugmug.com/Events/n-3SR2Rz/2025/Hightlights-2025/i-wpDqZtm/0/MbnTBhVRrFKHgT4hBdXz6dPbnGNWx7DD5X8LfSTJ9/M/20250625_144417-M.jpg)
```

## Local images

Local files live in `static/img/` and are WebP-only. **Never commit a raw
JPG.** All conversion is done with ImageMagick's `magick` (v7).

### Inspect before converting

Always look at the source first. It tells you whether the image needs
resizing, and — more importantly — what it is carrying.

```sh
magick identify -format '%wx%h  %b  profiles=%[profiles]\n' source.jpg
magick identify -format 'gps=%[EXIF:GPSLatitude]  make=%[EXIF:Make]\n' source.jpg
```

**Check the GPS line every time.** Phone photos embed the exact coordinates of
where they were taken, and `-strip` is the only thing removing them. The
wedding photo in this repo was a Pixel capture tagged
`gps=59°54.43' N` — the venue's location, published on a public blog. Stripped
output has no `EXIF` properties at all, so an empty result is the pass state:

```sh
magick identify -format 'gps=%[EXIF:GPSLatitude]\n' static/img/events/photo.webp
# gps=   <- good
```

### The conversion

```sh
command -v magick >/dev/null || { echo "ImageMagick 7 not installed"; exit 1; }

magick source.jpg \
  -auto-orient \
  -resize '1200x1200>' \
  -strip \
  -quality 80 \
  static/img/events/my-photo.webp
```

The output extension selects the format — there is no `-format` flag. Flags
apply left to right, so order matters: orient and resize read the source, then
`-strip` clears metadata from what is written.

| Flag | Why |
|---|---|
| `-auto-orient` | Applies the EXIF rotation tag before resizing, so phone photos don't end up sideways. Cheap insurance. |
| `-resize '1200x1200>'` | Fits within 1200×1200, preserving aspect ratio. See the `>` trap below. |
| `-strip` | Removes EXIF, GPS, XMP, and ICC. **The privacy step — never omit.** |
| `-quality 80` | WebP encoder setting. Beat re-encoded JPEG on every image tested. |

### The `>` trap

The trailing `>` means *only shrink*. Without it ImageMagick upscales, which
inflates the file for no visual gain:

```sh
magick 529x599.jpg -resize '1200x1200>'  t.webp   # 529x599   — correct
magick 529x599.jpg -resize '1200x1200'   t.webp   # 1060x1200 — wasted bytes
```

Other forms: `1200x>` caps width only, `x1200` caps height only, `50%` halves.
Portrait photos use height to reach the ceiling, so a 3000×4000 source at
`1200x1200>` becomes 900×1200.

### Re-encode from the original, never from a WebP

Each generation pass costs quality. Always convert from the original camera
or phone file. If the original is gone, do not re-compress the existing WebP —
keep it and move on.

### Batch

```sh
for f in raw-photos/*.jpg; do
  magick "$f" -auto-orient -resize '1200x1200>' -strip -quality 80 \
    "static/img/events/$(basename "${f%.jpg}").webp"
done
```

### Report before/after

Show the saving and get confirmation before deleting any source.

```sh
printf '%-28s %-11s %10s %10s\n' FILE DIMENSIONS BEFORE AFTER
for f in raw-photos/*.jpg; do
  w="static/img/events/$(basename "${f%.jpg}").webp"
  printf '%-28s %-11s %10s %10s\n' "$(basename "$f")" \
    "$(magick identify -format '%wx%h' "$w")" \
    "$(magick identify -format '%b' "$f")" \
    "$(magick identify -format '%b' "$w")"
done
```

`magick identify -format '%b'` prints a human-readable size (`189K`), which is
what you want in a report; use `%n` for the numeric value if you need to do
arithmetic.

### Expectations

The `static/img` set went from 4.15 MB to 531 KB once optimised. A single
unoptimised social card cost 2.99 MB at 3000×4000; at 1200px it is 189 KB.
Social cards are fetched by crawlers on every share, so always resize them.

### Referencing

Always pass `width` and `height`. Without them the browser cannot reserve
space and the page shifts as images load.

For a plain local image, use raw HTML to add lazy loading:

```html
<img src="/img/baking/sables.webp" loading="lazy" alt="Sablé cookies" width="1200" height="488">
```

## Social cards

Set a post-specific card when a local image fits the post:

```toml
[extra]
social_media_card = "/img/events/wedding-city-hall.webp"
```

Without it the site falls back to the avatar from `config.toml`, which is
almost never the right image. This is the one place a local file is preferred
over SmugMug — crawlers should not depend on a third-party host.

## Verifying

```sh
mise run verify
mise run build
```

Then confirm the rendered markup, since the minifier rewrites attribute
quoting and can hide a typo:

```sh
grep -oE '<meta[^>]*og:image[^>]*>' public/blog/my-post/index.html
grep -oE 'img/[a-z0-9./-]+' public/blog/my-post/index.html | sort -u
```

The first confirms the social card resolved; the second lists every local
image the page actually references. Zola appends a content hash to local asset
URLs, so expect a `?h=…` suffix.

Check the image actually loaded by loading the page; a wrong SmugMug size
segment 404s silently in the browser while still passing `zola check`.
