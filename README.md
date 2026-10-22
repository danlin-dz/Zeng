# Personal site — Danlin Zeng

One file, no build step. Open `index.html` in a browser to preview.

## Pages and links

- `/#home`
- `/#cv` — sections: `/#cv/education`, `awards`, `research`, `presentations`, `experience`, `outreach`, `skills`
- `/#about` — sections: `/#about/scuba`, `photography`, `running`, `hello`

Section links are shareable and land just below the top bar (or at the top of the photos, where photos sit beside the text).

## Updating the CV

Edit the CV section of `index.html`, then regenerate the PDF (below).

- **Publications** are numbered continuously across subsections via `counter-reset:cite N` on each `<ol>`. Adding or removing an entry means adjusting the offset on every later list.
- **Status lines:** `Published online [date]` (the date shown on the article page), `Accepted for publication [date]`, `Revised and resubmitted [month]`, `Submitted [month]`.
- **Links** go on their own line under the citation (`<p class="cite-tail">` with `class="doi"`): DOIs for published papers, `Preprint` for preprints. Link only official sources: journal or conference pages, and recipient lists for awards.
- **Presenting author** is underlined with `cite-pres`.
- **Conference abstract links:** wrap the meeting name in `<a class="doi">` inside `.venues`; it shows in blue with ↗.
- **Award links:** `<a class="awlink">` on the award title; it shows in black with ↗.
- Update the presentation totals line ("28 presentations in total…") when adding presentations.

## The downloadable CV

`Danlin-Zeng-CV.pdf` is printed from the CV page itself, so it always matches the site. To regenerate, open `/#cv` in Chrome, choose Print → Save as PDF, set paper to Letter, and turn background graphics on. The print stylesheet sets the margins, tightens spacing (8 pages), and hides navigation, the section rail, the footer and the download button.

Wait for the web fonts to load first. A PDF printed with fallback fonts won't match the site.

## Photos and videos

**Never edit uploaded photos or videos:** no filters, colour changes or effects. Allowed: cropping, scaling, trimming, transcoding and removing location metadata.

- Photos: about 1600 px on the long edge, saved in `images/`.
- Videos: ship each clip as H.264 `.mp4` and VP9 `.webm`, with no audio. The poster is the exact first frame, so nothing jumps when playback starts. iPhone HEVC files won't play in Chrome or Firefox, so always transcode:

```
ffmpeg -i in.mov -an -vf "scale=-2:1280" -c:v libx264 -crf 23 -pix_fmt yuv420p -movflags +faststart videos/x.mp4
ffmpeg -i videos/x.mp4 -an -c:v libvpx-vp9 -crf 32 -b:v 0 -row-mt 1 videos/x.webm
ffmpeg -i videos/x.mp4 -frames:v 1 -q:v 3 images/poster-x.jpg
```

- `images/run-3.jpg` recreates Strava's Year in Sport totals with the exact original figures (2024 vs 2023); it is not a screenshot.
- `images/home-sunset.jpg` is cropped only. Its positioning lives in the "HOME — sunset photo" CSS block.

## Slideshows

The markup is only the slides; ticks, caption, counter and arrows are generated:

```html
<div class="slideshow slideshow--tall" aria-label="What this shows">
  <div class="stage">
    <div class="slide" data-cap="Caption" data-file="images/thing.jpg">
      <img src="images/thing.jpg" alt="Description">
    </div>
  </div>
</div>
```

For a clip, replace the `<img>` with a muted, looping `<video playsinline>` that has a `poster` and both `<source>` files.

- `data-cap` is the caption. `data-file` is the path shown if the file is missing; keep it matching.
- Reorder slides by moving blocks.
- Stage shapes: default is 3:2 (diving), `slideshow--tall` is 3:4 (photography, running). Both shows in a side-by-side pair need the same class.
- Photos and clips both fill the stage (`object-fit: cover`), so anything not matching its shape gets cropped. Crop to the stage shape first.

## Journey map

Drawn on a canvas by the script at the end of `index.html`. The geometry is `scripts/journey-map-data.js` (Natural Earth, public domain), loaded only when the map scrolls near. Stops, captions and zoom levels are in the `STOPS` list. It pauses off-screen and shows a still overview when reduced motion is on.

## Design

- Fonts: Newsreader for headings, Inter for everything else, both from Google Fonts.
- The page is pinned to light mode (`color-scheme` meta, CSS, and a dark-mode override) so phones in dark mode don't repaint it. Keep these in the `<head>`.
- Icons: `favicon.svg`, `images/favicon-32.png`, `images/apple-touch-icon.png`.

## Deploying

Cloudflare Pages: connect the repo, leave the build command empty, and set the output directory to `/`. The link-preview tags already point to `https://danlinzeng.com/`.

`images/social-card.png` (the link preview, 1200×630) is the home-page sunset photo, cropped only, with the name in Newsreader and two lines in Inter set over the sky.
