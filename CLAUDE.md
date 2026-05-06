# CLAUDE.md

## Workflow

When the user provides a reference image (screenshot) and optionally some CSS classes or style notes:

1. **Generate** a single `index.html` file using Tailwind CSS (via CDN). Include all content inline — no external files unless requested.
2. **Screenshot** the rendered page using Puppeteer (`npx puppeteer screenshot index.html --fullpage` or equivalent). If the page has distinct sections, capture those individually too.
3. **Compare** your screenshot against the reference image. Check for mismatches in:
   - Spacing and padding (measure in px)
   - Font sizes, weights, and line heights
   - Colors (exact hex values)
   - Alignment and positioning
   - Border radii, shadows, and effects
   - Responsive behavior
   - Image/icon sizing and placement
4. **Fix** every mismatch found. Edit the HTML/Tailwind code.
5. **Re-screenshot** and compare again.
6. **Repeat** steps 3–5 until the result is within ~2–3px of the reference everywhere.

Do NOT stop after one pass. Always do at least 2 comparison rounds. Only stop when the user says so or when no visible differences remain.

## What this is

Static 3-page marketing website for Rulexo (rulexo.co.uk), an AI automation service for UK trade businesses. No build step, no framework, no dependencies to install. Open any `.html` file in a browser to preview locally.

## Stack

- **HTML + Tailwind CSS via CDN** — Tailwind is loaded from `https://cdn.tailwindcss.com` in each page's `<head>`. No config file.
- **`styles.css`** — shared custom styles loaded after Tailwind on every page. This is where CSS variables, component classes, and animations live.
- **Fonts** — Merriweather (headings) and Lato (body/UI) loaded from Google Fonts in each page's `<head>`.
- **Netlify Forms** — `contact.html` form uses `netlify` attribute and `data-netlify="true"`. The hidden `<input name="form-name">` field is required for Netlify's form detection to work.

## File structure

```
index.html      Home page
services.html   Services page
contact.html    Contact page (Netlify form)
styles.css      Shared custom CSS (variables, components, animations)
```

## CSS architecture

All colour tokens are CSS variables defined in `:root` in `styles.css`:

| Variable | Value | Use |
|---|---|---|
| `--bg` | `#0F0F0F` | Page background |
| `--surface` | `#161616` | Cards |
| `--accent` | `#2D6A4F` | Green — labels, buttons, highlights only |
| `--text` | `#FFFFFF` | Body text |
| `--muted` | `#888888` | Secondary text |

**Design rule:** `--accent` is never used as a background fill — only on text (`label-accent`, `step-number`), borders (`rule-accent`), and the hero glow. The `.btn-accent` button uses it as a background only because it is small and interactive.

Reusable component classes in `styles.css`: `.card`, `.btn-accent`, `.nav-link`, `.label-accent`, `.step-number`, `.rule-accent`, `.form-input`, `.hero-glow`, `.fade-in` / `.fade-in.visible`.

## Animations

Scroll-triggered fade-in is handled by a small `IntersectionObserver` snippet at the bottom of each page's `<body>`. Any element with class `fade-in` starts invisible and slides up when it enters the viewport. Stagger delays are applied inline with `style="transition-delay: 0.1s;"`.

## Shared nav pattern

Each page has an identical nav. The active page sets `class="nav-link active"` on its own link. When adding pages or renaming links, update the nav in all three HTML files.

## Deployment

Drop the folder into Netlify. No build command, no publish directory needed — set publish directory to `/` (or the folder root). Netlify Forms will auto-detect the form in `contact.html` on first deploy.
