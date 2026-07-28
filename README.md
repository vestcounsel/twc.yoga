# social-content

CSV-driven carousel generator. CSV content flows into the existing HTML slide
templates, SVG illustrations drop into the middle slides, Playwright renders
each slide in Chromium, and the result is final 1080 × 1350 PNG files.

This is an **HTML + SVG assets → PNG** workflow. The HTML is rendered and
screenshotted; nothing is converted to SVG along the way.

## Brand palette (TWC · The Whole Concept)

| Name     | Hex       | Role                                                                  |
| -------- | --------- | --------------------------------------------------------------------- |
| Words    | `#473C35` | text; the dark background (`bg-espresso`)                             |
| Ground   | `#DEDAD2` | primary light background (`bg-ground`)                                |
| Identity | `#9DB3BF` | blue-grey identity tone (the TWC logo's ground): `bg-haze`, soft badge washes, ambient dot fields |
| Teal     | `#0CA09D` | stronger brand step on light surfaces (titles, wordmark, rules)       |
| Sage     | `#8DC1B7` | stronger brand step on dark surfaces (titles, wordmark, rules)        |
| Accent   | `#0F98B9` / `#006793` | accent pair; the deep step is `bg-deep` and small-caps accent text |

The identity tone is deliberately ambient: it appears as translucent washes
behind subheading badges and as fading dot fields on every slide, so the
carousels share their ground with the logo.

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
- Backgrounds: `bg-ground`, `bg-haze`, `bg-espresso`, `bg-deep`.
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
