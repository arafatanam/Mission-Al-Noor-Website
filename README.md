# Mission Al Noor Website: Technical Documentation

A static, single-file website. No build step, no framework, no backend. One `index.html` holds the markup, styles, scripts and article data.

## 1. Stack

| Layer          | Choice                                                         |
| -------------- | -------------------------------------------------------------- |
| Markup         | HTML5, one file                                                |
| Styling        | Plain CSS with custom properties                               |
| Logic          | Vanilla JavaScript (ES6), no dependencies                      |
| Routing        | Hash-based, client side                                        |
| Fonts          | Google Sans (Google Fonts), IBM Plex Sans Arabic for Urdu text |
| Video          | YouTube iframe embeds                                          |
| Hosting target | Vercel (any static host works)                                 |

## 2. File structure

```
/
├── index.html     the whole site
├── pp.jpg         founder photo (optional, hidden if missing)
├── tsk.jpg        Shariat and Khidmat banner (optional, hidden if missing)
├── cv.pdf         founder CV, linked from the Founder view
└── README.md
```

Rename the local file to `index.html` before deploying. Keep all assets in the same folder. Paths are relative.

## 3. Page anatomy

Body is a flex column. Direct children, in DOM order:

1. `header` holds the large title (desktop only)
2. `p.tag` holds the subtitle
3. `#bar` is the sticky bar with `.row` (mobile title and menu button) and `nav#nav`
4. `main#main` receives the rendered view
5. `footer`
6. `button#up` is the fixed scroll-to-top button

Mobile reorders these with the CSS `order` property. Section 6 explains how.

## 4. Routing and rendering

Views switch through the URL hash. No page reloads, no server rules needed.

| Hash              | Content                                                            |
| ----------------- | ------------------------------------------------------------------ |
| `#home` (default) | Intro, founder card, three section cards, featured articles        |
| `#founder`        | Full profile, links, conference videos                             |
| `#tariqat`        | TaqwaTalks video series                                            |
| `#shariat`        | Shariat text with floated banner                                   |
| `#khidmat`        | Khidmat text with floated banner                                   |
| `#articles`       | Full article list                                                  |
| `#location`       | Founder location and contact                                       |
| `#<article-id>`   | One article (`award`, `book`, `dua`, `hadees`, `umrah`, `madinah`) |

Flow:

- `P` is an object. Each key maps to a function returning an HTML string for one view.
- `A` is an array of article objects: `{id, t, h}` for id, title and HTML body.
- `render()` reads `location.hash`. An article id match wins. Otherwise `P[hash]` runs. Unknown hashes fall back to home.
- `render()` writes the result into `#main`, rebuilds the nav, marks the active link with class `on`, closes the mobile menu and scrolls to the top.
- `hashchange` triggers `render()`. The first call runs on load.

Article tokens use plain letters. Avoid the reserved tokens `settings`, `sidebar`, `customize`, `upgrade`, `downloads` and anything starting with `comment-` if the page is ever served inside the Claude artifact viewer.

## 5. Data and helpers

```js
var A = [ {id:"dua", t:"Dua for Magfira", h:`<p>...</p>`}, ... ];
var O = ["award","book","dua","hadees","umrah","madinah"];
A.sort((x,y) => O.indexOf(x.id) - O.indexOf(y.id));
```

- `O` sets article importance. The list view follows this order. Home shows the first three entries as Featured articles.
- `list(n)` returns the first `n` articles as link rows.
- `vid(id, title)` returns a titled YouTube embed. The player carries its own "Watch on YouTube" control.
- `IMG` is the `tsk.jpg` banner tag. `onerror="this.remove()"` hides the banner when the file is missing.

### Add an article

1. Append `{id:"new-id", t:"Title", h:\`<p>Body</p>\`}`to`A`.
2. Add `"new-id"` to `O` at the wanted position. Entries missing from `O` sort to the front, so always list them.
3. Redeploy.

## 6. CSS architecture

### Design tokens

```css
:root {
  --bg: #e6f8d1;
  --s: #d6ebc3;
  --a: #d6ebc3;
  --t: #232820;
  --m: #3f4d33;
  color-scheme: light;
}
```

| Token        | Use                                                           |
| ------------ | ------------------------------------------------------------- |
| `--bg`       | Page background, hover fill on tinted surfaces                |
| `--s`, `--a` | Header, bar, footer, cards, hover fill on the page background |
| `--t`        | Body text                                                     |
| `--m`        | Muted text                                                    |

The palette is two greens plus a dark text color. The theme is light only. `color-scheme: light` stops browsers from forcing a dark rendering.

### Typography

Font stack: `"Google Sans", "Google Sans Flex", "IBM Plex Sans Arabic", system-ui, sans-serif`. Urdu glyphs fall through to IBM Plex Sans Arabic. Sizes use `clamp()` so type scales with the viewport.

### Layout

- `main`: `max-width: 56rem`, centered, `width: 100%` so every view has the same width.
- `html{scrollbar-gutter: stable}` reserves scrollbar space. Short pages no longer shift sideways.
- `body{min-height}` plus `main{flex:1}` pins the footer to the bottom on short views.
- Home cards: CSS grid. One column by default, three columns from `46rem`.
- Founder block: grid with `25% 1fr` columns. The photo container stretches to full height. The `img` is absolutely positioned with `object-fit: cover` and `object-position: center top`.
- Shariat and Khidmat banner: `float: left`, `width: min(16rem, 40%)`. Text wraps on the right and continues full width below. `main::after` clears the float.
- Videos: `.vid` uses `aspect-ratio: 16/9`.

### Breakpoint

One breakpoint: `46rem`. Desktop rules apply above. Mobile rules sit in `@media (max-width: 45.99rem)`.

### Sticky bar

`#bar` uses `position: sticky` with `top: env(safe-area-inset-top)` and `z-index: 20`. Padding is equal above and below. `body::before` is a fixed strip in the bar color that covers the device safe area on notched phones.

| Screen  | Behavior                                                                               |
| ------- | -------------------------------------------------------------------------------------- |
| Desktop | Title and subtitle scroll away. The centered nav stays pinned.                         |
| Mobile  | Title row (title left, menu button right) stays pinned. The subtitle scrolls under it. |

Mobile reordering:

- `header` is hidden. `#bar` gets `order: -2`. `.tag` gets `order: -1`.
- The `.row` shows `.bb` (compact title) and `#mb` (menu button).
- `nav` is hidden by default. Class `open` shows it as a stacked column with `max-height` and scrolling.

### Interaction states

- Every link in `main`, the footer and the logo gets a background hover.
- Links in the page background fill with `--a`. Links on tinted surfaces fill with `--bg`.
- `.btn` styles the four home buttons as rounded pills with an inverted hover.
- `#up` fades in after 300 px of scroll and scrolls smoothly to the top.

## 7. JavaScript behavior

| Function or handler | Role                                                                                     |
| ------------------- | ---------------------------------------------------------------------------------------- |
| `render()`          | Routing and DOM update                                                                   |
| `sc()`              | On scroll: toggles `#up` visibility (scrollY above 300) and the `stuck` shadow on `#bar` |
| `#mb` click         | Toggles `nav.open` and updates `aria-expanded`                                           |
| `#up` click         | `scrollTo({top:0, behavior:"smooth"})`                                                   |

`stuck` is true when `#bar` touches the viewport top and `scrollY` is above 0.

## 8. Video embeds

- Source: `https://www.youtube-nocookie.com/embed/<id>`
- Attributes: `loading="lazy"`, `allowfullscreen`, `referrerpolicy="strict-origin-when-cross-origin"`

**Error 153.** YouTube shows this when the host page sends no referrer. Opening the page from `file:///` causes it. Serve the site over `https` and the players load. If the error persists on the host, check for a `Referrer-Policy: no-referrer` header or a restrictive Content Security Policy. If embeds stay blocked, replace the iframe with a plain link.

Videos in use:

| View                   | IDs                                                                                    |
| ---------------------- | -------------------------------------------------------------------------------------- |
| Tariqat                | `5q61f7Bk3R4`, `SObPCXfLz6Y`, `FRoB6064fZ8`                                            |
| Founder (newest first) | `v3lbM0vnv8U` (2020), `Ns1e6iOGNBo` (2019), `M5AkrvKUikg` (2018), `jwlCad44-iE` (2016) |

## 9. Responsive and device support

- Viewport tag includes `viewport-fit=cover`. `:root` padding uses `env(safe-area-inset-*)` for notches and home indicators.
- The scroll-to-top button offsets itself by the bottom safe area.
- Long words and URLs wrap through `overflow-wrap: anywhere`.
- Urdu text blocks use `dir="rtl"`, `lang="ur"` and a taller line height.
- Tested layouts: phone (about 375 px), tablet, desktop. Verify on a real device before launch.

## 10. Accessibility

- Semantic elements: `header`, `nav`, `main`, `footer`, `article`.
- The menu button has `aria-label` and `aria-expanded`. The scroll button has `aria-label`.
- Images carry `alt` text. Iframes carry `title`.
- Link text is descriptive. Hover and focus rely on color change, so add a visible `:focus-visible` outline for keyboard users (see section 13).
- Text contrast on both backgrounds is high. Muted text uses `#3f4d33`.

## 11. Deployment on Vercel

1. Put `index.html`, `pp.jpg`, `tsk.jpg` and `cv.pdf` in one folder.
2. Push the folder to a Git repository or run `vercel` in the folder.
3. Pick the "Other" framework preset. Leave build command and output directory empty.
4. Test the three video players and the founder photo on the live URL.

No `vercel.json` is required. Hash routes never reach the server, so no rewrites are needed.

## 12. Performance

- Single request for HTML, two for font CSS, plus the font files.
- No JavaScript libraries. Script weight is under 3 KB of logic plus the content strings.
- Video iframes load lazily.
- Images are optional. Compress `pp.jpg` and `tsk.jpg` to under 300 KB each. Prefer WebP or optimized JPEG.

## 13. Known limitations and next steps

| Item              | Detail                                                                                                                                                                                        |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Missing content   | Biographies (Shaikh Abdul Qadir Jilani, Syed Abdul Bari Shah), Ramadan and Laylatul Qadr articles, prayer mistakes, Globe post, 2007 and 2008 travel schedules, Yusra Noor, Scandinavia visit |
| SEO               | Hash routes share one URL for crawlers. Add `<meta name="description">`, Open Graph tags and a `sitemap.xml`. Move to real pages if search traffic matters.                                   |
| Focus styles      | Add `:focus-visible` outlines to links and buttons                                                                                                                                            |
| Login and webmail | Removed. A static site has no backend for authentication                                                                                                                                      |
| Privacy           | Phone numbers and an email address appear in public text as written. Review them before launch                                                                                                |
| Font fallback     | Google Sans availability depends on Google Fonts. The stack falls back to `system-ui`                                                                                                         |
| Archive growth    | Past about 30 articles, move the article data into a JSON file and fetch it at load                                                                                                           |

## 14. Change log

| Area       | State                                             |
| ---------- | ------------------------------------------------- |
| Palette    | `#E6F8D1`, `#D6EBC3`, dark text                   |
| Theme      | Light only                                        |
| Font       | Google Sans                                       |
| Navigation | Sticky bar, hamburger menu on mobile              |
| Videos     | Inline embeds, no extra link below the player     |
| Media      | Founder photo slot, banner on Shariat and Khidmat |
