# Mission Al Noor Website

One static file. No build step, no framework, no backend. `index.html` holds the markup, styles, scripts and article data.

## Folder layout

```
/
├── index.html
├── cv.pdf                  linked from the Founder page
└── images/
    ├── favicon.svg
    ├── tsk.jpg             banner on the Shariat and Khidmat pages
    ├── founder.jpg         home hero (arch frame), Gallery
    ├── founder-2.jpg       home founder card, Gallery
    ├── founder-3.jpg       founder hero, Gallery
    ├── founder-4.jpg ...onwards    Gallery
    └── events/
        ├── 1976.jpg  1979.jpg  2008.jpg  2009.jpg  2016.jpg
        └── 2018.jpg  2019.jpg  2020.jpg  2023.jpg
```

Every image is optional. A missing file hides its slot. The home hero and founder card show the letters MNA when their photo is missing.

## Images and where they appear

| File                                      | Shows on                                        | Tips                                                     |
| ----------------------------------------- | ----------------------------------------------- | -------------------------------------------------------- |
| `images/founder.jpg`                      | Home hero, Gallery                              | Portrait, face in the upper half. The arch crops to 3:4. |
| `images/founder-2.jpg`                    | Home founder card, Gallery                      | Portrait or half length. Crops to 4:5.                   |
| `images/founder-3.jpg`                    | Founder page, Gallery                           | Any ratio. Shows full width at the top of the profile.   |
| `images/founder-4.jpg` to `founder-8.jpg` | Gallery                                         | Any ratio.                                               |
| `images/events/<year>.jpg`                | Journey (under that year), Gallery with caption | One photo per milestone. Year must match the list below. |
| `tsk.jpg`                                 | Shariat, Khidmat                                | Floats left beside the text.                             |

Keep each image under 300 KB. Use optimized JPEG or WebP. If you use WebP, change the extension in `index.html`.

## Routes

The site uses hash routing, so no server rules are needed. Each address is `your-domain/#name`.

| Address           | Page                                                                    |
| ----------------- | ----------------------------------------------------------------------- |
| `#home` (default) | Hero, stats, quote, founder card, three pillars, featured articles      |
| `#founder`        | Photo, full profile, links, conference videos                           |
| `#journey`        | Timeline of milestones with optional photos                             |
| `#tariqat`        | TaqwaTalks videos                                                       |
| `#shariat`        | Shariat text                                                            |
| `#khidmat`        | Khidmat text                                                            |
| `#articles`       | Full article list with reading times                                    |
| `#gallery`        | All photos in a masonry grid. Select a photo to enlarge. Esc closes it. |
| `#location`       | Founder location and contact                                            |
| `#<article-id>`   | One article: `award`, `book`, `dua`, `hadees`, `umrah`, `madinah`       |

Do not name an article `settings`, `sidebar`, `customize`, `upgrade` or `downloads`. Do not start one with `comment-`.

## How the code works

- `P` maps each route to a function that returns HTML.
- `A` is the article array. `O` sets article order.
- `E` is the timeline array. Each entry has `y` (year), `t` (title) and `d` (text).
- `fig(file, alt, caption)` builds an image that removes itself when the file is missing.
- `draw()` renders the page. Page changes crossfade through the browser View Transitions API.
- Stats on Home count up when the page loads. Timeline entries fade in as you scroll.

## Add an article

1. Append `{id:"new-id", t:"Title", h:\`<p>Body</p>\`}`to`A`.
2. Add `"new-id"` to `O` where you want it.

## Add a timeline event

1. Append `{y:"2025", t:"Title", d:"One or two sentences."}` to `E`. Keep the list in date order.
2. Drop `images/events/2025.jpg` in place.

Two events in the same year need different file names. Change the year string, for example `2025a`.

## Text layout

- Long paragraphs on Founder, Shariat, Khidmat, Location and all articles are justified and fill the full content width (`main>p,article p` in the CSS). Hyphenation is on, so word gaps stay even on phones.
- The Urdu block in the Dua article stays right aligned (`.ur`).
- To left align text, delete the `main>p,article p` rule.

## Design tokens

| Token  | Value     | Use                               |
| ------ | --------- | --------------------------------- |
| `--bg` | `#e6f8d1` | Page background                   |
| `--s`  | `#d6ebc3` | Cards, chips, footer              |
| `--l`  | `#f3fce8` | Nav, hover surfaces               |
| `--t`  | `#232820` | Body text                         |
| `--m`  | `#3f4d33` | Muted text, lines                 |
| `--d`  | `#2f3d25` | Buttons, active nav, progress bar |

Fonts: Cormorant Garamond (headings), Google Sans (body), Amiri (Arabic), Noto Nastaliq Urdu (Urdu). Light theme only.

## Breakpoints

- Below 62rem: the nav becomes a Menu button.
- Below 46rem: the hero, panels, founder card and timeline stack in one column.
