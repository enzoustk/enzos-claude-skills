# Extraction Guide — How to Extract Design Tokens

Use this reference during Step 4 (extraction). Claude uses `web_fetch` which returns static HTML/CSS. It does NOT render pages, execute JavaScript, or observe hover/scroll interactions.

## Extraction Reliability by Category

| Category | Reliability | Extractable from static HTML/CSS | Requires inference |
|----------|------------|----------------------------------|-------------------|
| Typography | HIGH | Google Fonts `<link>` tags, `@import` URLs, `font-family` in CSS, font weights | Rendered sizes (infer from `font-size` declarations) |
| Colors | HIGH | `:root` CSS custom properties, color hex/rgb in static CSS, inline styles | Hover/active colors (infer from `:hover` rules) |
| Spacing | MEDIUM | CSS variables, `padding`/`margin` in static CSS, `max-width` | Rendered gaps (infer from declared values) |
| Layout | MEDIUM | HTML structure, `max-width`, `grid`/`flex` declarations, media queries | Responsive behavior (infer from `@media`) |
| Components | MEDIUM | `border-radius`, `box-shadow`, button `padding` in CSS | Visual appearance without rendering |
| Imagery | LOW | `<img>` tags, `background-image` URLs, SVG presence | Photography style (infer from gallery) |
| Interactions | LOW | `transition`/`animation` in CSS, `@keyframes`, library imports in `<script>` | Hover feel, scroll behavior, cursor style |
| Motion | LOW | Keyframe definitions, easing values, duration/delay | Motion philosophy (infer from animation code density) |

## What to Extract and How

| Category | Tokens | Method |
|----------|--------|--------|
| **Typography** | Font families (display + body), sizes, weights, line-heights, letter-spacing | 1. `<link>` tags for Google Fonts → family + weights. 2. `@import` in CSS. 3. `font-family` in `:root` and static declarations. 4. `font-size` from CSS rules on headings/body. |
| **Colors** | Backgrounds (base, elevated), accent (primary, hover, subtle), text (primary, secondary, tertiary), borders | 1. `:root` custom properties (`--color-*`, `--bg-*`). 2. Color hex/rgb from CSS on `body`, cards, buttons. 3. Tailwind config or theme object in inline scripts. |
| **Spacing** | Base unit, section paddings, gaps, container max-widths | 1. `padding`/`margin` from CSS on sections/containers. 2. Identify recurring values for base unit. 3. `max-width` from wrapper classes. 4. `gap` from flex/grid. |
| **Imagery** | Photography style, illustration style, icon style | 1. `<img>` tags and `background-image` URLs. 2. SVG patterns. 3. Gallery descriptions for style. Mark `[GALLERY]`. |
| **Interactions** | Hover effects, scroll behavior, animation style | 1. `transition`, `animation`, `@keyframes` in CSS. 2. `<script>` imports: GSAP, Framer Motion, Lenis, Three.js. 3. `:hover` rules for hints. Mark `[INFERRED]`. |
| **Layout** | Grid, section rhythm, container widths, hero layout | 1. HTML section structure. 2. Grid/flex + `max-width`. 3. Hero: centered vs split from HTML. 4. Media queries. |
| **Components** | Card/button/nav styles | 1. `border-radius`, `box-shadow`, `border` on card/button/nav selectors. 2. Button `padding` for proportions. |

## Difficult Extraction Scenarios

### CSS-in-JS (Styled Components, Emotion, CSS Modules)

Styles inject as `<style>` tags with hashed class names. If server-rendered (Next.js, Remix, Gatsby SSR), these tags are in the fetched HTML — parse for `font-family`, colors, padding (ignore class names). If client-rendered, `<style>` tags are absent. Fallback: search for public Storybook, design system docs, npm packages with tokens, or GitHub repos. Mark as `[PARTIAL]`.

### Obfuscated/Minified CSS

Minified CSS is still valid — values are readable even with hashed selectors. Search for `font-family`, hex values, padding/margin values within the minified output.

### Cloudflare/Bot Protection

1. Try `web_fetch` directly
2. Wayback Machine: `web.archive.org/web/[URL]`
3. Gallery listing page (Awwwards SOTD shows fonts, colors, tech stack, screenshots)
4. Search: `"[site name] design system"`, `"[site name] brand guidelines"`, `"[site name] fonts"`
5. If all fail: mark as `[GALLERY]`, extract from gallery page only

### SPA with Client-Side Rendering

Fetched HTML may be just `<div id="root">`. Try: CSS files linked in `<head>` (fetch directly), `manifest.json`/`asset-manifest.json` for CSS paths, common paths (`/static/css/main.[hash].css`). Search for public design system or Storybook. Mark as `[PARTIAL]` if only CSS extractable.

### Proprietary/Paid Fonts

Identify the font from `font-family`, search for closest Google Fonts equivalent matching x-height, weight range, and character. Document both: "Reference uses [Proprietary], substituted with [Google Font]."

Common substitutions:

| Proprietary | Google Fonts Substitute |
|-------------|----------------------|
| Helvetica Neue | Inter |
| Futura | Poppins, Nunito Sans |
| Avenir | Nunito |
| Proxima Nova | Montserrat |
| Circular | Plus Jakarta Sans |

## Edge Cases Handled in This Guide

### Site is down or blocks extraction (Edge Case 2)

`web_fetch` → Wayback Machine → gallery page data → search for published design system/brand guidelines. If all fail: drop reference, use remaining ones. If it was the only reference: ask user for alternative. Document: `"[URL] — blocked, [GALLERY] only"`.

### CSS-in-JS without CSS variables (Edge Case 3)

Server-rendered → parse `<style>` tags. Client-rendered → search Storybook, docs, GitHub, npm tokens. Last resort: gallery inference. Mark: `[PARTIAL — CSS-in-JS, visual inference for: [categories]]`.

### SPA client-side rendering (Edge Case 4)

Try fetching CSS files directly from `<head>` links or manifest. Search for public design system. Awwwards SOTD pages list tech stack and fonts. Fallback: gallery inference, marked `[GALLERY]`.
