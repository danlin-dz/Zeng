# Personal site — Danlin Zeng

One file, no build step. Open `index.html` in a browser to preview.

## What to fill in

| Where | What |
|---|---|
| `YOUR_ID` in the Strava link | Your Strava profile |
| DOIs | Two are live. Add more as papers are accepted — link text and `href` both. |
| `VIDEO_ID_1/2/3` | YouTube IDs — the part after `watch?v=`. Thumbnails load automatically from the ID. |
| `images/…` | Photos. Filenames appear on-screen as placeholders until the files exist. |
| `videos/dive-1.mp4` … `dive-3.mp4` | Short clips. Silent, looping, plays only while on screen. |

## Asset list

**Still needed** — `images/`

- `run-2.jpg` — Vancouver Sun Run, **square crop** (buy the clean download; see note below)
- `portrait.jpg` — 3:4, for the contact block

Resize to about 1600 px on the long edge first. The originals off a TG-6 are 4000×3000 and roughly 4 MB each, far more than a web page needs.

**Already in place** — `videos/` and `images/`

- `dive-clip-1.mp4` — whale shark, 9s
- `dive-clip-2.mp4` — whale shark from below, 8s
- `dive-clip-3.mp4` — green sea turtle, 4s
- `poster-1.jpg` … `poster-3.jpg` — first frames, shown while each clip loads
- `run-1.jpg` — BKTC mile time trial, McCarren Park
- `run-3.jpg` — Strava Year in Sport card, cropped to 3:4
- `dive-reef.jpg`, `dive-boat.jpg`, `dive-turtle.jpg`, `dive-parrotfish.jpg` — 4:3
- `photo-1.jpg` … `photo-9.jpg` — the photography set, in the order you sent them

H.264 MP4, no audio, under about 3 MB each. Note that H.265/HEVC — what iPhones and DJI cameras record by default — will not play in Chrome or Firefox, so everything has to be transcoded.

Encoding a clip down to web size:

```
ffmpeg -i input.mov -an -vf "scale=-2:900" -c:v libx264 -crf 26 -movflags +faststart videos/dive-1.mp4
```

`-an` strips audio, `+faststart` lets playback begin before the whole file arrives.

## Slideshows

Each hobby has two: **Stills** and **Clips**, side by side on desktop and stacked on phones. Each runs on its own timer and hands control over the moment you touch it.

The markup is only the slides. Ticks, caption, counter, and arrows are generated, so adding a slideshow anywhere means writing this and nothing else:

```html
<div class="slideshow" aria-label="What this shows">
  <span class="label show-label">Stills</span>
  <div class="stage">
    <div class="slide" data-cap="Caption" data-file="images/thing.jpg">
      <img src="images/thing.jpg" alt="Description">
    </div>
    <!-- more .slide blocks -->
  </div>
</div>
```

For a clip, swap the `<img>` for:

```html
<span class="clip-badge">Loop</span>
<video muted loop playsinline preload="metadata" poster="images/poster-x.jpg">
  <source src="videos/thing.mp4" type="video/mp4">
</video>
```

- **Captions** live in `data-cap`
- **`data-file`** is what shows in the placeholder slot if the file is missing — keep it matching the real path
- **Reordering** is just moving blocks; nothing references position
- Media fits rather than crops, so portrait and landscape can share one stage
| Strava figures | Typed by hand — the Strava API needs OAuth, which a static site cannot do. |

Photos: drop into `images/`. Landscape shots crop to 4:3, the portrait crops to 3:4. Anything 1600 px wide is plenty.

## URLs

- `/#home`
- `/#cv` — and `/#cv/research`, `/#cv/education`, `/#cv/outreach` jump straight to a section
- `/#about`

Section links are shareable, so you can send a program director straight to `/#cv/research`.

## Before it goes live

`index.html` contains two placeholder URLs in the Open Graph tags — `https://example.com/`. Replace both with the real domain, otherwise the link preview image will not resolve when someone pastes the URL into an email. Everything else works from a relative path and needs no change.

`images/social-card.png` is a baked image, so its type does not come from the stylesheet. It was generated with a Times-like serif as a stand-in for Newsreader. If you want it to match the site exactly, rebuild it in any design tool at 1200×630 using Newsreader for the name — the layout is centred, name at roughly 116 px, rule beneath, two lines under that.

## Deploying

Cloudflare Pages: new project → connect the repo → leave the build command empty → output directory `/`. Same as Eyelingual.

## Printing

`Cmd/Ctrl + P` on the CV page produces a clean PDF — nav, rail, and film thumbnails are stripped out by the print stylesheet.


## Two notes on the running assets

**The Sun Run photo.** What you sent is a photo of a screen showing MarathonPhoto's proof: the watermark runs across you, there is app chrome in the frame, and screen moire is visible at full size. Buy the digital download from the race photo site and save it as `images/run-2.jpg` — the slot is already wired and will fill itself in. Until then that slide shows a labelled placeholder.

**The Strava figures.** Transcribed from the Year in Sport card: 701 km, 5,771 m, 75 hours, 76 days active. The card compares against 2023, which makes it the 2024 Year in Sport rather than 2025, so the block is titled accordingly — correct it if that is wrong. The percentage changes were left off; against a near-zero 2023 baseline they say more about when you started than about the year itself.


## Video formats

Each clip ships as both `.mp4` (H.264) and `.webm` (VP9), listed in that order. Every mainstream browser takes the MP4; the WebM exists for Chromium builds compiled without proprietary codecs, which otherwise show a blank frame. Keep both when you add running clips:

```
ffmpeg -i clip.mp4 -an -c:v libvpx-vp9 -crf 34 -b:v 0 -deadline good -cpu-used 3 -row-mt 1 clip.webm
```


## Stage shapes

Each show declares the shape of the media it holds, so nothing crops and nothing bars:

| Class | Stage | Used by |
|---|---|---|
| *(none)* | 3:2 | dive stills, dive clips |
| `slideshow--square` | 1:1 | running stills, running clips |
| `slideshow--tall` | 3:4 | photography |

Both shows in a pair must carry the same class, or the two stages end up different heights and the row stops being aligned.

Stills use `object-fit: cover`, so an image fills the stage edge to edge and anything that does not fit the ratio gets cropped rather than letterboxed. Feed each stage images that match its shape and nothing is lost. Clips still use `contain`, because cropping video is worse than a band of background.

Practical consequence: **`run-2.jpg` needs to be a portrait crop.** The Sun Run proof is landscape, so crop it to 3:4 around yourself before saving it, or the sides will be cut off.


## Why the page is pinned to light

Android Chrome and a number of in-app browsers (Instagram, LinkedIn, some mail clients) will automatically repaint a light page dark when the phone is in dark mode, unless the page says which schemes it supports. Three things prevent it here:

- `<meta name="color-scheme" content="light">`
- `html { color-scheme: light }`
- a `prefers-color-scheme: dark` block that re-declares the light background and text

`<meta name="theme-color">` also tints the browser's own address bar to match the paper colour. Do not remove these when editing the head.


## CV content

Everything on the CV page comes from `Danlin_Zeng_Web.docx`: 16 manuscripts (2 published, 11 under review, 3 in preparation) and 24 conference presentations (11 oral, 13 poster).

- Publication numbering runs continuously 01&ndash;16 across the three subsections, set by `counter-reset:cite N` on each `<ol>`. Insert or remove an entry and the following lists need their offset adjusted.
- Presenting author is underlined via `cite-pres`; the CARO poster is the only one not marked, per the document.
- Where one piece of work went to several meetings, the venues are grouped as a `<ul class="venues">` inside the citation.
- **Not carried over:** the phone number from the CV header. See below.
- **Still placeholder:** the whole Outreach section. The document has no outreach content.


## Typography

Two families only, loaded from Google Fonts:

- **Newsreader** &mdash; display serif, used for the name, page titles, and section headings (`--display`)
- **Inter** &mdash; everything else: body copy, navigation, labels, captions, dates, DOIs (`--body`)

`--mono` is kept as a variable name but points at Inter, so there is one text face across the whole site. Small labels sit at 0.72&ndash;0.76rem with light tracking rather than the tighter, wider-tracked settings used earlier; captions and the footer name are sentence case.
