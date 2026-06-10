# Project Tablet — Devlog Skeleton

A Jekyll site styled like a typeset LaTeX paper ([latex.css](https://latex.vercel.app/))
with [KaTeX](https://katex.org/) equations, an RSS feed, and a paper-title-page
homepage. Built to deploy on GitHub Pages with **zero build configuration** —
GitHub runs Jekyll for you on every push.

## Architecture: which pages are static, which are Jekyll

- **`index.html` (homepage)** — 100% static HTML, no front matter, no Liquid.
  Jekyll copies it through untouched, and VS Code's **Live Server** previews it
  exactly as deployed. Edit it like any HTML file you've ever written.
- **`blog/` and `_posts/`** — Jekyll territory. The blog index loops over posts
  automatically and posts get wrapped in layouts, so these only render through
  Jekyll (push to GitHub, or run Jekyll locally — see below). Live Server will
  show raw template tags on these; that's expected, not broken.
- **Trade-off to know:** because the homepage doesn't use `_layouts/default.html`,
  its nav and footer are a hand-maintained copy. If you change the nav/footer in
  the layout, mirror the change in `index.html`.

## Previewing the homepage with Live Server

1. Install the **Live Server** extension in VS Code (by Ritwick Dey).
2. Open the repo folder in VS Code.
3. Right-click `index.html` → **Open with Live Server** (or click "Go Live" in
   the status bar).
4. The page opens at `http://127.0.0.1:5500/` and hot-reloads on every save.
   latex.css and KaTeX load from CDNs, so equations and styling all work —
   what you see is what GitHub Pages will serve.

To preview the **blog** locally you need Jekyll running (see "Local preview"
below) — or just push and check the live site; builds take ~1 minute.

## Deploy in 5 minutes (recommended path: new repo)

1. Create a new GitHub repo for the game (e.g. `tablet`). Keep it separate from
   your wyrcethrang.com repo — you said the game gets its own brand.
2. Copy everything in this folder into the repo root. Commit and push.
3. In `_config.yml`, set:
   ```yaml
   baseurl: "/tablet"            # <-- your repo name
   url: "https://YOURUSER.github.io"
   ```
4. On GitHub: **Settings → Pages → Source: Deploy from a branch → main / (root)**.
5. Wait ~1 minute. Your site is at `https://YOURUSER.github.io/tablet/`.

When you buy a domain for the game later: add it under Settings → Pages →
Custom domain, then set `baseurl: ""` and `url: "https://yourdomain.com"`.
Nothing else changes — every internal link uses `relative_url`.

## Theme: "Tablet in the Case"

The paper-white page floats on a dark fired-umber ground — the artifact lit in
a dim gallery — under a glazed-brick band (lapis + gold hairline). The palette
is picked from the actual artifacts:

| Token | Hex | Source |
|---|---|---|
| `--lapis` | `#1f4e79` | Ishtar Gate glaze — links, structure |
| `--gold` / `--gold-ink` | `#c9a227` / `#8f6f14` | processional gold — eyebrows, accents |
| `--ground` / `--ground-deep` | `#3a2a1e` / `#241a11` | the dark gallery wall behind the page |
| `--clay` | `#8f5d42` | fired clay — on-paper ornament (dividers) |
| `--cream` | `#f5ecdc` | text on the dark ground |
| `--paper` | `#ffffff` | the document itself |

The ground has a faint SVG grain so it reads as surface rather than flat
color (delete the first `background-image` layer in `html` to remove it) and
a soft radial spotlight behind the hero.

Ornament comes from three reusable elements, stacked as registers on the
homepage (gate → frieze → seal band → paper, like the monument itself):

- **`.gate`** — the lapis glazed-brick field (faint running-bond joints baked
  in as a tiling SVG; delete the first `background-image` layer to remove).
- **`.frieze`** — the gold rosette band from the Ishtar Gate, 18px,
  symmetric gold hairlines.
- **`.seal`** — a cylinder-seal impression: repeating **𒊺 𒆬 𒇽** (ŠE
  *barley*, KUG *silver*, LU₂ *person* — grain, silver, labor) in gold on
  dark fired clay. Always full page width. Blog pages share the homepage's
  register architecture via the layouts: slim gate (nav) → frieze → seal →
  paper sheet(s) → footer band. Posts get a second seal between the article
  and the post-navigation sheet:

```html
<div class="seal" aria-hidden="true">𒊺 𒆬 𒇽 𒊺 𒆬 𒇽 ... (repeat ~36x)</div>
```

### Gallery mode (the homepage)

The homepage uses `<body class="gallery">` and stacks like the monument:
the **gate** (lapis brick field holding nav + hero with gold CTAs), the
**rosette frieze**, a full-width **seal band**, then the paper sheet at the
same measure as the blog. A second full-width seal band slices through the
sheet at the Status break — the sheet is split into `.sheet.flow-top` and
`.sheet.flow-bottom` (open bottom/top edges) so the paper reads as continuous
behind the band. Images inside stay quiet: full column width, hairline
border, italic caption:

```html
<figure class="panel">
  <img src="assets/img/your-capture.png" alt="What the screenshot shows">
  <figcaption>One-line caption.</figcaption>
</figure>
```

Two placeholder SVGs ship in `assets/img/` (a trade map and the price-spike
chart). Swap them for real captures as the prototype grows — 16:9 PNGs or
short GIFs both work; keep files under ~10 MB. The same `.panel` pattern works
inside blog posts.

Tuning and reverting (all in `assets/css/custom.css`):
- **Clay margins too wide on the blog?** Uncomment the `max-width` line in the
  `body` block (the "WIDTH KNOB" comment) and tune to taste.
- Too warm? Lighten `--clay` (e.g. `#a3765a`).
- Want plain white pages back? Delete the `html`, `body`, and `body::before`
  blocks at the top of the file — everything else is independent.
- Titles use **Cinzel** (Google Fonts); body text stays Latin Modern. Never
  add a "Papyrus-style" font, and never set running text in cuneiform.

## Writing a post

1. Copy `_posts/2026-06-09-temple-feedback-controller.md`.
2. Rename it `YYYY-MM-DD-your-slug.md` — the date in the filename is the
   post date and must match the front matter.
3. Edit the front matter (`title`, `date`, `number`, `excerpt`) and write
   Markdown. Push. Done — the blog index, prev/next links, and RSS feed all
   update automatically.

### The one math gotcha (read this!)

Markdown is processed *before* KaTeX, and Markdown treats `_` as italics.
So:

- **Inline math**: single dollars, simple symbols only — `$G$`, `$p$`,
  `$\delta$`. No subscripts inline.
- **Display math**: wrap in a plain `<div>`, which tells Markdown to keep
  its hands off:
  ```html
  <div>
  $$p_t = \bar{p}\left(\frac{D_t}{S_t}\right)^{\varepsilon}$$
  </div>
  ```
  Inside the `<div>`, write any LaTeX you like — subscripts, cases, matrices.

## Customizing

| What | Where |
|---|---|
| Game name / tagline / author | `_config.yml` (feeds blog pages + RSS) **and** hardcoded in `index.html` |
| Homepage content | `index.html` (plain static HTML) |
| Accent colors, proposition boxes | `assets/css/custom.css` |
| Nav links, footer, email signup | `_layouts/default.html` (blog pages) **and** `index.html` (homepage) |
| Analytics (GoatCounter) | commented placeholders in both `<head>`s |
| Email signup (Buttondown) | commented placeholders in both footers |

The homepage "Proposition" boxes are reusable anywhere (including posts):

```html
<div class="proposition" data-name="Dear Bread">
  <p>The claim, stated in one italic sentence.</p>
  <p class="remark">Why it's true, in plain text.</p>
</div>
```

They auto-number in page order.

## Images and GIFs

Create `assets/img/`, drop files in, and use the `<figure>` pattern at the
bottom of the example post. Keep GIFs under ~10 MB so pages stay fast; for
anything longer, upload the video to the Reddit/Twitter post itself and use
a still + link here.

## Local preview (optional)

You don't need this — pushing to GitHub builds the site. But if you want a
local preview: install Ruby, then

```bash
gem install bundler jekyll
bundle init && bundle add jekyll jekyll-feed
bundle exec jekyll serve
```

and open http://localhost:4000.

## What's already wired

- **RSS** at `/feed.xml` (jekyll-feed) with autodiscovery in the page head —
  this is how the systems-game crowd will actually follow you.
- **KaTeX** auto-render with a fallback that survives Markdown-engine quirks.
- **Mobile**: latex.css is responsive; display equations scroll instead of
  overflowing.
- **SEO basics**: per-page titles and meta descriptions from your excerpts.
