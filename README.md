# Esteban Cuenca — Personal Site

Portfolio site for film & immersive producer Esteban Cuenca. No build step.

---

## Beat Studio has moved

This site previously included an experimental Web Audio hover-synth and a spatial 3D beat studio. That entire system — audio engine, hover-synth, beat studio, and the related hardware controller CAD work — has been extracted into its own repository for independent development:

**[github.com/estebanjcuenca/beat-studio](https://github.com/estebanjcuenca/beat-studio)**

This repo is now a clean, audio-free professional portfolio.

---

## Structure

- `index.html` — markup; loads `content.md` and renders the CV
- `fonts.css` — PP Radio Grotesk `@font-face` rules (base64-embedded)
- `styles.css` — all other CSS (tokens, layout, dark mode, print, responsive)
- `print.css` — A4/print export layout (standard, one-page, DRIFT/100% Film/EAVE variants)
- `content.md` — all site content (markdown + frontmatter). **Edit this to change content.**
- `photo.jpg` — hero photo, referenced via `photo:` in the frontmatter
- `privacy.html` — standalone privacy policy page
- `disc-atlas.html` — moved to the `beat-studio` repo

## How it works

On load, the JS fetches `content.md`, parses the frontmatter (`photo`, `subtitle`, `location`, `email`, `privacy`) and markdown body, and renders the DOM.

## Local preview

Because content is fetched at runtime, the site must be served over HTTP — opening `index.html` directly from the file system will show a load error.

```bash
python3 -m http.server      # then open http://localhost:8000
```

Or use the Live Server extension in Cursor/VS Code.

## ⚠️ Note for tool-assisted editing

`index.html` has one script tag: `<script type="application/ld+json">` in `<head>` (SEO). Content lives in `content.md`, not embedded in HTML. All CV rendering, print/export, and A4 layout logic lives in the inline `<script>` at the end of `<body>`.

## Design system

- Font: PP Radio Grotesk (Regular, Italic, Black) — base64-embedded in `fonts.css`
- CSS tokens: `--ink` `--bg` `--mid` `--line` `--pad-x`
- Dark mode: `html.dark` class, toggled via `◐` button, persisted in `localStorage` (key `ec-dark`)
- Layout: desktop = text left + sticky photo right; mobile = photo top, text below

## CV export variants

The toolbar (visible in export mode) offers several print/export layouts, driven by markers in `content.md`:

| Variant | Trigger | Marker in `content.md` |
|---|---|---|
| Standard (multi-page) | Default export | — |
| One-page | `1 pg` button | — |
| DRIFT | `DRIFT` button | `## {drift} ...` section |
| 100% Film | `100%` button | `## {100film} ...` section |
| EAVE motivation statement | `EAVE` button | `## {eave} ...` section |
| EAVE-IPP applicant notes | populated automatically alongside EAVE | `## {eave-ipp} ...` section |

Rows tagged `{dev}` in a table only render on non-production hosts (see `PROD_HOSTS` in `index.html`).

## Features

- Markdown-driven content rendering (fetched from `content.md`)
- Dark/light mode with localStorage persistence
- Section hide/show with localStorage persistence
- Scroll fade-in animations (IntersectionObserver)
- Print CSS (A4, standard/one-page/DRIFT/100% Film/EAVE variants, hides UI, respects hidden sections)
- Mobile responsive layout
- SEO: meta description, Open Graph tags, JSON-LD Person schema

## TODO

- [x] **Replace `https://yourdomain.com` placeholders** — `https://estebanjcuenca.github.io/esteban-cuenca-site/`
- [x] Extract base64 photo to `photo.jpg` — done 2026-06-10
- [x] Extract markdown content to `content.md` — done 2026-06-10
- [x] Deploy to GitHub Pages — `https://estebanjcuenca.github.io/esteban-cuenca-site/`
- [x] Remove audio/beat-studio system — moved to `beat-studio` repo

## Deploy (GitHub Pages)

```bash
git init
git add .
git commit -m "Initial commit: single-file portfolio site"
git branch -M main
git remote add origin git@github.com:USERNAME/REPO.git
git push -u origin main
```

Then: repo → Settings → Pages → Source: "Deploy from a branch" → branch `main`, folder `/ (root)` → Save. Site appears at `https://USERNAME.github.io/REPO/` within a minute or two.
