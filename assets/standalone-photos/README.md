# Standalone photos

Drop standalone photo posts here. Each file in this folder is just a hosted
image — nothing in the repo processes it. You grab a direct link to the file
and paste that link into the Excel sheet that feeds the scheduler.

This folder is separate from `assets/photos/`, which holds photos that are
placed *inside* carousel slides (e.g. the bio portrait).

## How to add a photo

1. Upload the image (JPG or PNG) into this folder — on GitHub:
   **Add file → Upload files** while inside this folder.
2. Use a descriptive, lowercase, hyphenated filename with no spaces, e.g.
   `2026-08-studio-morning-light.jpg`. Avoiding spaces keeps the URL clean.
3. Commit (and merge to `main` if you uploaded on a branch).

## Getting the link for Excel

The direct-file URL follows this pattern:

```
https://raw.githubusercontent.com/vestcounsel/twc.yoga/main/assets/standalone-photos/<filename>
```

Or, from the file's page on GitHub, click **Raw** (or right-click the Raw
button → Copy link address) and paste that into the sheet.

Note: the link only works once the file is on `main`, and the repo must be
accessible to whatever fetches the link — if the scheduler can't read a
private repo, the photo won't load.

## Sizing

Feed posts are 1080 × 1350 (4:5 portrait), same as the carousel slides.
Uploads don't need to be pre-cropped, but keep the long edge at 1080 px or
more so nothing gets upscaled.
