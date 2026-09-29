---
name: blog-media
description: Add photos, SmugMug galleries, floated images, or video to posts, and convert local images to WebP. Use when embedding a gallery, choosing an image size, setting a social media card or og:image, or converting an image's format.
---

# Images, galleries, and video

Shortcode signatures are in `AGENTS.md`; image rules (WebP, 1200px, quality 80,
strip metadata) are there too. This is the procedure.

## Local or SmugMug?

Almost everything photographic is hosted on SmugMug — use a hot link. Only
commit a local file when the image has no SmugMug equivalent.

| Situation | Approach |
|---|---|
| A trip, event, or album worth revisiting | `smugmug` gallery |
| A photo beside a paragraph of prose | `floated_image` |
| A single photo standing alone, or a post's opener | Plain `![]()` — the theme already centres it |
| A social card, avatar, or page illustration | Local in `static/img/` |
| A sequence of photos that *are* the post | Plain `![]()` images |

Plain images need no centring CSS. `themes/tabi/sass/parts/_image.scss` sets
`display: block; margin: 0 auto` on `img`, so a bare `![]()` is centred and
capped to the column. Reach for `floated_image` only when text is meant to
wrap beside the image — a standalone opener that floats is a bug, not a style.

## SmugMug size selection

The size is encoded in the URL path and the filename must match it — SmugMug
does not resample on request, so `/M/…-S.jpg` 404s. Request the size that
matches the display slot; asking for `L` and showing it at 400px wastes
bandwidth on every visitor.

| Slot | Size | Path segment |
|---|---|---|
| Floated beside text | `S` (~400px) | `/S/…-S.jpg` |
| Gallery thumbnail | `M` (~600px) | `/M/…-M.jpg` |
| Full-width figure | `L` (~1024px) | `/L/…-L.jpg` |

## Gallery embeds

The standard shape for a photo-heavy trip: a floated image to draw the eye into
the opening, then a linked gallery after the intro. From
`content/blog/2025-06-14_fjord-cruise-oslo.md`:

```text
{{< floated_image src="https://photos.smugmug.com/photos/i-Mx7qGGv/0/LnBvGKHtN9FSDtBK6vxsqH7hkgcFPsN9qz2NM52nP/S/i-Mx7qGGv-S.jpg" float="right" alt="Ingunn &amp; André on The Fjord" width="400" />}}

Opening prose, two or three paragraphs, no heading.

{{< smugmug path="Events/2025/Fjord-Cruise-Oslo/n-NfGRw4" caption="Click to view the Fjord Cruise Oslo photo &amp; video gallery" thumbnail="https://photos.smugmug.com/photos/i-WtXqHHG/0/NF3bKJ9x9BmWx6jnKTQt4qkcFFhF3m8Z7hxgBGMJP/M/i-WtXqHHG-M.jpg" width="600" height="450" alt="Fjord Cruise Oslo" />}}

The post then continues with its own sections.
```

`path` is the gallery path after `gallery.aendu.rocks`; `thumbnail` is any
photo from that gallery at the right size. Caption it as an invitation —
"Click to view the …" — since the click-through is the point.

For a run of photos, plain markdown images often beat a gallery:

```text
![Friends from Switzerland](https://photos.smugmug.com/Events/n-3SR2Rz/2025/Hightlights-2025/i-wpDqZtm/0/MbnTBhVRrFKHgT4hBdXz6dPbnGNWx7DD5X8LfSTJ9/M/20250625_144417-M.jpg)
```

`floated_image` defaults to `float="left"`, `width="300"`, and drops the float
below 600px, so it is safe anywhere in prose. Write ampersands in shortcode
attributes as `&amp;`; it renders as `&`.

## Converting a local image

ImageMagick is not part of the pinned toolchain, so check before relying on it:

```sh
command -v magick >/dev/null || { echo "ImageMagick 7 not installed"; exit 1; }
```

Convert from the original camera or phone file — never re-encode an existing
WebP, since each pass costs quality.

```sh
magick source.jpg -auto-orient -resize '1200x1200>' -strip -quality 80 \
  static/img/events/my-photo.webp
```

The output extension selects the format; there is no `-format` flag. Flags
apply left to right, so `-auto-orient` and `-resize` must read the source before
`-strip` clears metadata.

| Flag | Why |
|---|---|
| `-auto-orient` | Applies the EXIF rotation tag so phone photos don't end up sideways. |
| `-resize '1200x1200>'` | Fits within 1200×1200, preserving aspect ratio. |
| `-strip` | Removes EXIF, GPS, XMP, ICC. **The privacy step — never omit.** |
| `-quality 80` | WebP encoder setting; beat re-encoded JPEG on every image tested. |

**The `>` is load-bearing.** It means *only shrink*. Without it ImageMagick
upscales, inflating the file for no visual gain:

```sh
magick 529x599.jpg -resize '1200x1200>'  t.webp   # 529x599   — correct
magick 529x599.jpg -resize '1200x1200'   t.webp   # 1060x1200 — wasted bytes
```

Other forms: `1200x>` caps width only, `x1200` caps height only, `50%` halves.
A 3000×4000 portrait at `1200x1200>` becomes 900×1200.

### Batch and report

```sh
for f in raw-photos/*.jpg; do
  magick "$f" -auto-orient -resize '1200x1200>' -strip -quality 80 \
    "static/img/events/$(basename "${f%.jpg}").webp"
done

printf '%-28s %-11s %10s %10s\n' FILE DIMENSIONS BEFORE AFTER
for f in raw-photos/*.jpg; do
  w="static/img/events/$(basename "${f%.jpg}").webp"
  printf '%-28s %-11s %10s %10s\n' "$(basename "$f")" \
    "$(magick identify -format '%wx%h' "$w")" \
    "$(magick identify -format '%b' "$f")" "$(magick identify -format '%b' "$w")"
done
```

`%b` gives a human-readable size (`189K`), which is what a report wants; use
`%n` when you need to do arithmetic. Report the saving and get confirmation
before deleting any source.

## Verifying rendered output

`zola check` does not catch a wrong SmugMug size segment — the page builds and
the image 404s silently in the browser. Build and inspect:

```sh
mise run build
grep -oE '<meta[^>]*og:image[^>]*>' public/blog/my-post/index.html
grep -oE 'img/[a-z0-9./-]+' public/blog/my-post/index.html | sort -u
```

Zola appends a content hash to local asset URLs, so expect a `?h=…` suffix.
Confirm a converted image is metadata-free — an empty result *or* a lookup
error both mean the EXIF block is gone:

```sh
magick identify -format 'gps=%[EXIF:GPSLatitude]\n' static/img/events/my-photo.webp
```
