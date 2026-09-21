# social-content

CSV-driven carousel generator. CSV content flows into the existing HTML slide
templates, SVG illustrations drop into the middle slides, Playwright renders
each slide in Chromium, and the result is final 1080 × 1350 PNG files.

This is an **HTML + SVG assets → PNG** workflow. The HTML is rendered and
screenshotted; nothing is converted to SVG along the way.

## Brand palette (TWC · The Whole Concept)

Four background surfaces, and the text and accent color each one carries.
`CLAUDE.md` holds the authoritative version of this table plus the rotation
rules; keep the two in step.

| Class     | Hex       | Name | Text on it      | Accent on it   |
| --------- | --------- | ---- | --------------- | -------------- |
| `bg-teal` | `#0CA09D` | teal | navy `#0B3954`  | navy `#0B3954` |
| `bg-sage` | `#8DC1B7` | sage | navy `#0B3954`  | deep `#006793` |
| `bg-deep` | `#006793` | deep | light `#F5DEB3` | sage `#8DC1B7` |
| `bg-mist` | `#9DB3BF` | mist | navy `#0B3954`  | navy `#0B3954` |

**A carousel is a single color.** The cover, every middle slide, and the
closing sit on the same surface, so `cover_background`, `middle_background`,
and `closing_background` all carry the same value on every row of a post.

**The color changes from post to post**, walking
`bg-teal → bg-sage → bg-deep → bg-mist` in publish order. Consecutive posts
never repeat a surface, and sage never follows mist (they are 1.08:1 apart and
read as the same color two posts running). `npm run generate` fails if either
rule is broken.

The previous palette's `bg-ground`, `bg-haze`, and `bg-espresso` are retired
along with coral `#FF6F61`. A CSV row still using one of those names stops
generation with the replacement named in the error.

Surfaces are flat: no dot fields, no corner texture, no gradients. The only
tint is the translucent wash behind a middle slide's subheading badge.

## Structure

```
social-content/
├── assets/illustrations/   SVG illustrations used on middle slides
├── assets/photos/          photos placed inside slides (e.g. the bio portrait)
├── assets/standalone-photos/  standalone photo posts, linked from the scheduler sheet
├── templates/
│   ├── cover.html          cover slide template
│   ├── middle.html         middle slide template
│   └── closing.html        closing (CTA) slide template
├── content/
│   ├── posts.csv           carousel content, one row per middle slide
│   └── cta-library.json    approved closing headings and subheadings
├── scripts/generate.js     the generator
└── public/social/          generated PNGs, grouped by month and post
```

Each template opens directly in a browser for visual review — it falls back
to sample content whenever its `{{PLACEHOLDERS}}` have not been replaced.

## Setup

```
npm install
npx playwright install chromium
```

## Adding illustrations

Drop SVG files into `assets/illustrations/` and reference them by filename in
the `middle_illustration` CSV column (e.g. `document.svg`). Leave the field
empty for a slide without an illustration. SVGs are placed as-is — never
renamed, redrawn, or rasterized ahead of time.

## Editing content/posts.csv

One row per **middle** slide. Rows sharing a `post_id` form one carousel;
repeat the carousel-level fields (cover, CTA, publish time) on every row.

Columns:

```
post_id,publish_at,cover_title,cover_subtitle,cover_background,slide_number,
middle_heading,middle_body,middle_illustration,middle_alt,middle_background,
cta_key,closing_background,middle_subheading
```

- `publish_at` uses `YYYY-MM-DD HH:MM`; its month decides the output folder.
- Write `\n` inside a field for an explicit line break.
- Backgrounds: `bg-teal`, `bg-sage`, `bg-deep`, `bg-mist`. One per carousel,
  the same value in all three background columns; the surface rotates between
  posts. See the rules in `CLAUDE.md`.
- `slide_number` orders the middle slides and must be unique per post. It is
  internal only — slide numbers are never displayed on a slide.
- `middle_subheading` is an optional bold line (e.g. a person's name) placed
  between the divider and the body.
- `cover_subtitle` is a legacy column; the generator ignores it entirely.

## CTA keys

The closing slide's heading and subheading come from
`content/cta-library.json`, selected by the `cta_key` column. Add new closing
language there; an unknown key stops generation with an error, so only
approved copy ships.

## Generating PNGs

```
npm run generate
```

The generator validates every carousel, renders each slide in Chromium, checks
for overflow and element collisions, and writes PNGs to:

```
public/social/YYYY-MM/<post_id>/slide-01.png   (cover)
public/social/YYYY-MM/<post_id>/slide-02.png   (middle slides…)
public/social/YYYY-MM/<post_id>/slide-NN.png   (closing, always last)
```

`node scripts/generate.js --post <post_id>` regenerates a single carousel.

Every PNG is exactly 1080 × 1350.

`npm run preview` writes the filled-in HTML to `.tmp/rendered/` without
launching Chromium, so slides can be inspected in a browser first.

## Watch mode

```
npm run watch                       # watch everything
npm run watch -- --post <post_id>   # focus on one post
```

While it runs, editing `output/<post>/html/slide-NN.html` re-renders just
that slide's PNG in place within about a second, and editing a template or
`content/` file re-runs the full generator. The single-slide path is a fast
preview without the generator's overflow/collision validation — run
`npm run generate` before shipping.
