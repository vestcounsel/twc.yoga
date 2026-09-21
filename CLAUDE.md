# TWC brand settings (base instructions)

Authoritative brand rules for this project. These apply to every slide,
carousel, and template here. Copy rules live in `content/style-guide.md`;
this file governs color.

## Background palette

Four surfaces. These are the only background colors in the brand.

| Class     | Hex       | Name | Luminance |
| --------- | --------- | ---- | --------- |
| `bg-teal` | `#0CA09D` | teal | 0.277     |
| `bg-sage` | `#8DC1B7` | sage | 0.472     |
| `bg-deep` | `#006793` | deep | 0.118     |
| `bg-mist` | `#9DB3BF` | mist | 0.432     |

There is no coral and no espresso surface. The old `bg-ground`, `bg-haze`,
and `bg-espresso` classes are retired; the generator rejects them by name
and points at the replacement.

## Backgrounds alternate

Backgrounds rotate through the four surfaces in this order:

```
bg-teal  →  bg-sage  →  bg-deep  →  bg-mist  →  (wraps to bg-teal)
```

The rotation runs across the whole carousel in slide order: cover, then each
middle slide by `slide_number`, then the closing. Each carousel starts one
step further along the rotation than the one published before it, so the feed
does not open on the same surface every time.

Two rules are enforced by `scripts/generate.js` and generation fails if either
is broken:

1. **No two consecutive slides share a surface.**
2. **Sage never touches mist.** They are only 1.08:1 apart and read as one
   unbroken surface across a slide change. This is why `bg-deep` sits between
   them in the rotation rather than at the end.

## Text and accent per surface

Navy `#0B3954` is the text color on the three lighter surfaces; `#F5DEB3` is
the text on deep. Every pairing below is measured against the surface and
clears 3:1 at the display sizes these templates use (the smallest body copy is
40px regular, the smallest accent 24px bold).

| Surface   | Text            | Ratio | Accent / wordmark | Ratio |
| --------- | --------------- | ----- | ----------------- | ----- |
| `bg-teal` | navy `#0B3954`  | 3.78  | navy `#0B3954`    | 3.78  |
| `bg-sage` | navy `#0B3954`  | 6.05  | deep `#006793`    | 3.11  |
| `bg-deep` | light `#F5DEB3` | 4.75  | sage `#8DC1B7`    | 3.11  |
| `bg-mist` | navy `#0B3954`  | 5.58  | navy `#0B3954`    | 5.58  |

The accent drives the middle-slide title and rule, and the cover and closing
wordmark. On every surface the wordmark is full opacity.

Dot fields and subheading washes are drawn from the same four surfaces at low
alpha, never from a color outside the palette.

## Adding a color

Don't. If a new surface is genuinely needed, it has to clear 3:1 against navy
or against `#F5DEB3`, and it has to sit at least 1.5:1 away from every surface
it can be adjacent to in the rotation. Add it to the table above, to the
`:root` block in all three templates, and to `BACKGROUNDS` in
`scripts/generate.js` in the same change.
