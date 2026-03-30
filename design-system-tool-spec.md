# Design System Tool — High-Level Specification

This document specifies the architecture, flow, and deliverables of the `design-system-tool` skill. It is a blueprint — the actual `SKILL.md` and reference files will be written from this specification.

---

## A) Position in the Ecosystem

### Flow diagram

```
User provides brand context (name, industry, tone, competitor URLs)
       ↓
┌─────────────────────────────────────────────────────────┐
│  design-system-tool                                     │
│  Step 1: Collect context                                │
│  Step 2: Research references (galleries + web search)   │
│  Step 3: Present references to user (GATE)              │
│  Step 4: Extract design tokens from approved sites      │
│  Step 5: Assemble design system (md + css)              │
│  Step 6: Validate and prepare handoff                   │
└─────────────────────────────────────────────────────────┘
       ↓ produces: design-system/design-system.md + design-system.css + references-report.md
       ↓
┌─────────────────────────────────────────────────────────┐
│  front-end-craft                                        │
│  Reads design-system/design-system.md → overrides 1-3   │
│  (colors, typography, spacing, motion identity)         │
│  Reference files remain valid as TECHNIQUE LIBRARIES    │
└─────────────────────────────────────────────────────────┘
       ↓ defines HOW it looks and feels
       ↓
┌─────────────────────────────────────────────────────────┐
│  landing-page-craft                                     │
│  Reads design-system/design-system.md → overrides       │
│  section selection, layout choices, hero/footer patterns │
│  Loads front-end-craft for visual execution              │
└─────────────────────────────────────────────────────────┘
       ↓ defines WHAT to build
       ↓
Final output: reference-derived interface or landing page
```

### Dependency rules

| Skill | Depends on | What happens if absent |
|-------|-----------|----------------------|
| **design-system-tool** | Nothing — runs first | Both build skills fall back to internal prescriptive defaults. Output is functional but uses "AI signature" aesthetics (same fonts, effects, patterns every time). |
| **front-end-craft** | Optionally consumes design-system-tool output | If design system exists: uses it as primary source. If absent: uses its own tables and defaults. If front-end-craft itself is absent: landing-page-craft uses hardcoded minimum defaults (Space Grotesk + Inter, dark theme, Cinematic, #3B82F6 accent). |
| **landing-page-craft** | Loads front-end-craft; optionally consumes design-system-tool output | Works standalone for non-LP interfaces (dashboards, apps). No degradation when LP skill is absent. |

### Key principle

Every skill in the chain works independently with graceful degradation. The design-system-tool **adds uniqueness** — it is never required.

### Output location convention

All design-system-tool outputs go to a `design-system/` directory in the project root (or working directory):

```
[project root]/
└── design-system/
    ├── design-system.md          ← primary source of truth
    ├── design-system.css         ← CSS tokens for implementation
    └── references-report.md      ← analysis notes
```

**Detection by build skills:**
- front-end-craft checks for `design-system/design-system.md`
- landing-page-craft checks for `design-system/design-system.md`
- If the file exists → load it. If not → proceed with defaults.

**This path is the CONTRACT.** Both the design-system-tool output and the build skill detection must use this exact path.

---

## B) Inputs and Outputs

### Inputs from user

| Input | Required? | Format | If missing |
|-------|-----------|--------|------------|
| Brand name | Yes | Text | Ask — non-negotiable |
| Industry / vertical | Yes | Text (e.g., "joalheria", "SaaS B2B", "clínica odontológica") | Ask — needed for gallery searches |
| Target audience | Recommended | Text description of who visits | Infer from industry |
| Tone / personality | Recommended | Keywords: luxury, tech, playful, editorial, bold, minimal, corporate | Infer from brand name + industry |
| Competitor or inspiration URLs | Optional | 1-5 URLs the user admires or wants to surpass | Skip — rely on gallery research |
| Existing brand assets | Optional | Logo, hex colors, font names, brand guidelines PDF | Skip — extract everything from references |
| Conversion goal (if LP) | Optional | WhatsApp, signup, booking, purchase, phone | Passed to landing-page-craft, not used by this skill |
| What they like / dislike about competitors | Optional | Free text ("I like X's typography but hate their colors") | Skip — present neutral options in Step 3 |

### Outputs of the skill

| Deliverable | Format | Location | Consumed by |
|-------------|--------|----------|-------------|
| `design-system.md` | Structured markdown with all design tokens, sourced and documented | `design-system/` | front-end-craft (Steps 1-3), landing-page-craft (section selection, layout, hero pattern) |
| `design-system.css` | CSS custom properties file with all tokens as variables | `design-system/` | front-end-craft (Step 6, implementation — copy into project) |
| `references-report.md` | Summary of all analyzed references: URLs, what was extracted, what was incomplete, conflict resolutions | `design-system/` | User review, debugging, future reference |

---

## C) 6-Step Flow

### Step 1: Collect project context

- **Purpose:** Gather all inputs needed to construct targeted research queries
- **Inputs:** User's message, any provided URLs or brand assets
- **Outputs:** Structured brief:
  ```
  Brand: [name]
  Industry: [vertical]
  Audience: [description]
  Tone: [2-3 keywords]
  Existing constraints: [fonts/colors if provided, or "none"]
  Competitor URLs: [list or "none"]
  User preferences: [likes/dislikes if mentioned]
  ```
- **Agent decides:** Infer tone from brand name + industry when not stated. Infer audience from industry when not stated.
- **User decides:** Confirm or correct all inferences. Provide missing required inputs (brand name, industry).
- **Advance when:** Brand name + industry confirmed. At least tone OR audience defined.
- **Fallback:** If user provides minimal info (e.g., "make me a site for my jewelry store"), infer: industry=jewelry, audience=people looking to sell gold, tone=luxury/trust. Present inferences explicitly: "I'm inferring the tone as luxury/trust based on the jewelry industry — correct?" Proceed only after confirmation.

### Step 2: Research high-level references

- **Purpose:** Find 5-8 candidate reference sites from curated galleries and web search
- **Inputs:** Structured brief from Step 1
- **Outputs:** Candidate list (5-8 sites), each with:
  - URL
  - Source (which gallery or search)
  - Relevance note (why this site fits)
  - Visual category (dark/light, minimal/rich, typographic/image-heavy)
  - Brief description of design approach
- **Agent decides:** Which galleries to search, query construction (see `references/research-sources.md`), result evaluation, diversity enforcement
- **User decides:** Nothing — this is research. User reviews in Step 3.
- **Advance when:** At least 5 candidates found from at least 2 different sources. No more than 2 candidates with near-identical aesthetics (same color scheme + same layout + same font style).
- **Fallback:** If fewer than 5 results for the exact industry:
  1. Expand to adjacent industries (jewelry → luxury retail → premium e-commerce → luxury services)
  2. If still insufficient, use general searches: "best [dark/light] landing page [current year]" filtered by tone keywords
  3. Flag to user: "I found references from adjacent industries because [industry] has limited gallery representation. These share the same tone and aesthetic quality."

### Step 3: Present references to user (GATE — human approval)

- **Purpose:** User selects which references to derive the design system from
- **Inputs:** Candidate list from Step 2
- **Outputs:** Approved reference list (1-4 sites)
- **Agent decides:** Recommend top 3 with brief justification. Flag potential issues ("this site uses experimental layout that may not transfer well to a conversion-focused LP").
- **User decides:** APPROVE or REJECT each candidate. Can add their own URLs.
- **Advance when:** At least 1 reference approved (see quantity rules in Section D)

#### How to present references

1. **Provide the clickable URL** for each reference
2. **Describe the design in specific terms**, not generics:
   - ❌ "Beautiful site with modern design"
   - ✅ "Dark background (#0A0A0A), large serif typography (appears to be Playfair Display), monochromatic palette with gold accent, purely typographic hero with no images, smooth scroll with subtle reveals"
3. **Instruct the user to open the links** before approving: "Open each link in your browser to see the design in action — a text description does not replace the visual experience."
4. **For each reference, highlight what's worth extracting:** "From this site, the most valuable elements for your project are: [color system / typography / hero treatment / motion design]"

- **Fallback:** If user rejects ALL candidates:
  1. Ask specifically what they disliked (too dark? too minimal? wrong industry feel?)
  2. Re-run Step 2 with adjusted queries incorporating feedback
  3. Allow max 2 retry cycles
  4. If still no approval after 2 retries: ask user to provide their own reference URLs, OR proceed without design system (flag "AI signature" risk explicitly: "Without references, the output will use my default aesthetic choices — the same fonts and effects I always use.")

### Step 4: Extract design tokens from approved sites

- **Purpose:** Fetch each approved site's HTML/CSS and extract typography, colors, spacing, layout, components. Infer motion and imagery from code patterns and gallery data.
- **Inputs:** Approved reference URLs (1-4 sites)
- **Outputs:** Per-site extraction document:
  ```
  ## Site: [URL]
  ### Typography
  - Display font: [family], weights [list] — [PARSED]
  - Body font: [family], weights [list] — [PARSED]
  - Scale: hero [N]px, h2 [N]px, body [N]px, small [N]px — [PARSED/INFERRED]
  - Line-heights: [values] — [PARSED/INFERRED]

  ### Colors
  - Background base: [hex] — [PARSED]
  - Background elevated: [hex] — [PARSED]
  - Accent: [hex] — [PARSED]
  - Text primary: [hex] — [PARSED]
  - Text secondary: [hex] — [PARSED]
  - Text tertiary: [hex] — [PARSED/INFERRED]
  - Borders: [hex] — [PARSED]

  ### Spacing
  - Base unit: [N]px — [INFERRED]
  - Section paddings observed: [list] — [PARSED]
  - Container max-width: [N]px — [PARSED]

  ### Motion
  - Libraries detected: [GSAP/Framer Motion/Lenis/Three.js/none] — [PARSED]
  - Transition properties found: [description] — [PARSED]
  - Hover behavior: [description] — [INFERRED]
  - Scroll behavior: [description] — [INFERRED]
  - Immersion level estimate: [Subtle/Cinematic/Maximal] — [INFERRED]

  ### Layout
  - Hero: [centered/split/etc.] — [PARSED]
  - Grid: [description] — [PARSED/INFERRED]
  - Section rhythm: [padding variation notes] — [INFERRED]

  ### Imagery
  - Approach: [typographic-only/product photography/illustrations/mixed] — [PARSED/GALLERY]

  ### Extraction completeness
  - [COMPLETE/PARTIAL — list what's missing and why]
  ```
- **Agent decides:** Extraction method per site (see `references/extraction-guide.md`). Map extracted values to token categories. Choose appropriate method tag for each token.
- **User decides:** Nothing — technical extraction
- **Advance when:** At least typography + colors successfully extracted from each approved site. Missing categories (motion, imagery) are documented with method tags.
- **Fallback:** If a site blocks extraction:
  1. Try `web_fetch` first (direct HTML/CSS fetch)
  2. If Cloudflare/403/timeout: check Wayback Machine for cached version
  3. Check the gallery page (Awwwards SOTD pages show fonts, colors, screenshots)
  4. Search: `"[site name] design system"`, `"[site name] brand guidelines"`, `"[site name] fonts"`
  5. If all fail: mark as `[GALLERY]` extraction only, extract what's visible from gallery descriptions
  6. If it was the only approved reference: ask user for an alternative URL

**Extraction honesty rule:** Mark every token with its extraction method: `[PARSED]` (extracted directly from CSS/HTML code), `[INFERRED]` (derived from code patterns, library presence, or CSS rules that suggest but don't guarantee the behavior), or `[GALLERY]` (sourced from gallery listing, screenshots, or third-party descriptions). The build skills treat `[PARSED]` as high-confidence and `[INFERRED]`/`[GALLERY]` as suggestive — they may apply their own defaults if the inference seems weak.

### Step 5: Assemble documented design system

- **Purpose:** Synthesize per-site extractions into one coherent design system
- **Inputs:** Per-site extraction documents from Step 4
- **Outputs:** `design-system/design-system.md` + `design-system/design-system.css`
- **Agent decides:** Conflict resolution when references disagree (see `references/consolidation-rules.md`):
  1. **Majority rule:** If 2+ of 3 references agree, use the majority value
  2. **Tone fit:** If all differ, choose the value that best matches the stated tone/audience
  3. **User preference:** If user expressed likes/dislikes in Step 1, weight accordingly
  4. Document every conflict and resolution reasoning
- **User decides:** Nothing directly — but the output is reviewable in Step 6
- **Advance when:** Design system covers all 7 categories:
  1. Typography (display + body fonts, scale, weights)
  2. Colors (full palette with roles)
  3. Spacing (base unit, scale, section padding tiers)
  4. Imagery (approach and style notes)
  5. Interactions (hover philosophy, scroll behavior, recommended immersion level)
  6. Layout (container widths, grid, hero layout)
  7. Anti-pattern exceptions (which anti-patterns are overridden, with justification)
- **Fallback:** If the design system is incomplete (e.g., no motion patterns extractable from any reference):
  1. Fill gaps with front-end-craft defaults
  2. Mark each section as `[REFERENCE-DERIVED]` or `[DEFAULT — no reference data]`
  3. The build skills handle partial design systems — they use design system values where available, internal defaults elsewhere

### Step 6: Consolidate and prepare handoff

- **Purpose:** Validate the design system, produce the references report, write files to `design-system/`
- **Inputs:** Design system from Step 5 + all extraction notes
- **Outputs:** Final `design-system/design-system.md`, `design-system/design-system.css`, `design-system/references-report.md`
- **Agent decides:** Run quality checklist (Section I). Flag WCAG contrast failures. Flag anti-pattern exceptions that lack strong reference justification. Auto-fix contrast issues (darken/lighten text to meet 4.5:1).
- **User decides:** Review final design system. Approve, request adjustments, or override specific tokens.
- **Advance when:** Quality checklist passes. User approves (or accepts with noted caveats).
- **Fallback:** If quality checklist fails:
  1. Contrast issues: automatically adjust failing tokens (darken/lighten text), document: "Adjusted [hex] → [hex] to meet WCAG AA 4.5:1 (reference value was [original])"
  2. Missing sections: flag as incomplete, proceed with `[DEFAULT]` markers
  3. User rejects the design system: discuss specific objections, adjust tokens manually, re-validate

---

## D) Research Strategy

### Curated gallery sources (in priority order)

| Priority | Source | URL | Strength | Best for |
|----------|--------|-----|----------|----------|
| 1 | Awwwards | awwwards.com | Highest quality, numerically scored | Any industry — filter by category, check scores ≥8.0 |
| 2 | Godly | godly.website | Curated modern/cutting-edge design | Tech, SaaS, agency, minimal aesthetics |
| 3 | SiteInspire | siteinspire.com | Large curated collection, filterable | Any industry — filter by style + subject |
| 4 | Lapa.ninja | lapa.ninja | Landing pages specifically | Landing page references — filter by color/industry |
| 5 | Landingfolio | landingfolio.com | LP-specific, organized by industry | Industry-specific LP patterns |
| 6 | SaaS Landing Page | saaslandingpage.com | SaaS-specific | SaaS products, B2B, developer tools |
| 7 | Minimal Gallery | minimal.gallery | Clean, minimal designs | Luxury, editorial, refined brands |
| 8 | Httpster | httpster.net | Diverse styles, curated | Eclectic styles, creative brands |
| 9 | One Page Love | onepagelove.com | Single-page sites | Single-page LPs and portfolios |

### Query construction patterns

**Tier 1 — Queries that work consistently with web search:**

| Pattern | Example |
|---------|---------|
| `"[industry] website" awwwards` (no `site:` operator) | `"jewelry website" awwwards` |
| `"best [industry] website design [year]"` | `"best jewelry website design 2025"` |
| `"[industry] landing page design inspiration"` | `"jewelry landing page design inspiration"` |
| `"[industry] website" godly OR awwwards OR siteinspire` | Combines multiple galleries in one search |
| `"[competitor name] website"` (to find real URL) | `"tiffany website"` → get actual URL for fetch |

**Tier 2 — Queries for refining after initial results:**

| Pattern | Example |
|---------|---------|
| `"[site name from results] design case study"` | `"acme jewelry design case study"` |
| `"[industry] website design" behance case study` | Finds case studies with design explanation |
| `"[tone] website design" [year] award` | `"minimal luxury website design 2025 award"` |

**Tier 3 — Fallback when industry has low gallery representation:**

| Pattern | Example |
|---------|---------|
| `"[adjacent industry] website design" awwwards` | `"luxury retail website design" awwwards` |
| `"best [tone] landing page [year]"` | `"best dark minimal landing page 2025"` |
| `"[industry] branding" behance` | Finds branding projects with links to the live site |

**Alternative (may work inconsistently):** `site:awwwards.com [industry]` — gallery pages use AJAX and internal filters, so `site:` operators return unpredictable results. Use as a supplement, not primary method.

**After finding mentions of specific sites in articles or lists, use `web_fetch` on the actual site URL for extraction.**

### Acceptance criteria for references

A reference is ACCEPTED if it meets ALL of these:
- **Professional design:** Not a template (ThemeForest, Wix, Squarespace default), not a student project
- **Current:** Design feels modern — within approximately 2 years. No dated patterns (skeuomorphism, early-2010s flat design)
- **Quality score:** ≥ 8.0 on Awwwards (if sourced from Awwwards). Other galleries: editorial judgment based on typography quality, spacing consistency, and visual hierarchy
- **Relevant:** Same industry OR same tone/aesthetic as the target project
- **Extractable:** Site has enough content/sections to derive a meaningful design system (not just a splash page or countdown timer)

### Rejection criteria

Reject a reference if ANY of these apply:
- Template site (identifiable ThemeForest/Wix/Squarespace template)
- Obvious design issues (broken responsive, poor contrast, inconsistent spacing, text overflow)
- Purely experimental/art (no commercial design intent — art installations, interactive experiments)
- Older than ~3 years with visibly dated patterns
- Near-duplicate of an already-selected reference (same color scheme + layout + font style)

### Quantity and diversity rules

- **Minimum:**
  - Standard: 2 references (preferred: 3 — two from industry, one from adjacent for diversity)
  - Single-reference mode: 1 reference is acceptable if the user explicitly confirms: "This single reference is exactly what I want as the design direction." In this case, skip consolidation (Step 5 simplifies to direct token mapping) and flag in design-system.md: `**Mode:** Single-reference — less diversity in design decisions.`
- **Maximum:** 6 references (beyond 6, extraction becomes diminishing returns and consolidation becomes complex)
- **Diversity requirement:** No more than 2 references with near-identical aesthetics. At least 1 must differ in either:
  - Color theme (dark vs light)
  - Layout approach (minimal vs rich)
  - Typography style (serif vs sans-serif, geometric vs humanist)

---

## E) Extraction Strategy

### Extraction reliability by category

Claude uses `web_fetch` which returns static HTML/CSS. It does NOT render pages, execute JavaScript, or observe hover/scroll interactions. This table defines what is reliably extractable vs what requires inference:

| Category | Reliability | What Claude CAN extract (from static HTML/CSS) | What requires inference |
|----------|------------|------------------------------------------------|----------------------|
| Typography | HIGH | Google Fonts `<link>` tags, `@import` URLs, `font-family` in CSS declarations, font weights in CSS | Exact rendered sizes (infer from CSS `font-size` declarations) |
| Colors | HIGH | CSS custom properties in `:root`, color hex/rgb values in static CSS, inline styles | Hover/active state colors (infer from `:hover` rules in CSS) |
| Spacing | MEDIUM | CSS variables for spacing, `padding`/`margin` values in static CSS, `max-width` declarations | Actual rendered gaps (infer from declared values in CSS) |
| Layout | MEDIUM | HTML structure, container `max-width` in CSS, `grid`/`flex` declarations, media queries | Responsive behavior (infer from `@media` breakpoints) |
| Components | MEDIUM | `border-radius`, `box-shadow`, button `padding` in static CSS | Visual appearance without rendering |
| Imagery | LOW | `<img>` tags, `background-image` URLs, SVG presence, `<picture>` elements | Photography style, visual weight (infer from gallery descriptions) |
| Interactions | LOW | `transition` and `animation` properties in CSS, `@keyframes` definitions, library imports (GSAP, Framer Motion, Lenis, Three.js) in `<script>` tags | Actual hover feel, scroll behavior, cursor style |
| Motion | LOW | Keyframe definitions, easing values in CSS, animation duration/delay | Overall motion philosophy (infer from density and type of animation code) |

### What to extract from each site

| Category | Tokens to extract | Extraction method |
|----------|------------------|-------------------|
| **Typography** | Font families (display + body), sizes (hero, h2, h3, body, small, overline), weights used, line-heights, letter-spacing | 1. Check `<link>` tags for Google Fonts URLs → extract family names and weights. 2. Check `@import` in CSS for font URLs. 3. Check `font-family` in `:root` variables and static CSS declarations. 4. Extract `font-size` values from CSS rules targeting headings, body, and small text. |
| **Colors** | Background (base, elevated, interactive), accent (primary, hover, subtle), text (primary, secondary, tertiary), borders (subtle, active) | 1. Check `:root` CSS custom properties (`--color-*`, `--bg-*`). 2. If no custom properties: extract color hex/rgb values from static CSS rules on `body`, card classes, button classes, text elements. 3. Look for Tailwind config or theme object in inline scripts. |
| **Spacing** | Base unit, section padding values, element gaps, container max-widths, card padding | 1. Extract `padding`/`margin` values from CSS declarations on sections and containers. 2. Identify recurring values to determine the spacing base unit (e.g., multiples of 8px). 3. Extract `max-width` from container/wrapper classes. 4. Extract `gap` values from flex/grid declarations. |
| **Imagery** | Photography style (product, lifestyle, abstract, none), illustration style, icon style | 1. Check `<img>` tags and `background-image` URLs for image presence and type. 2. Check for SVG icons and illustration patterns. 3. For style assessment: check gallery listing page description and screenshots. Mark as `[GALLERY]`. |
| **Interactions** | Hover effects, scroll behavior, animation style, cursor approach | 1. Search for `transition`, `animation`, `@keyframes` in fetched CSS. 2. Check `<script>` tags for library imports — GSAP, Framer Motion, Lenis, Three.js, ScrollTrigger. 3. Extract `:hover` rules from CSS for hover behavior hints. 4. Infer motion philosophy from the density and type of animation code found. Mark as `[INFERRED]`. |
| **Layout** | Grid system (columns, gaps), section rhythm (padding variation), container width variation, hero layout | 1. Analyze HTML structure for section patterns and container nesting. 2. Extract grid/flex declarations and `max-width` values. 3. Identify hero layout from HTML structure (centered content vs split columns). 4. Note media queries for responsive breakpoints. |
| **Components** | Card style (border-radius, shadow, border), button style (padding, border-radius, shadow), nav style (height, background), footer structure | 1. Extract `border-radius`, `box-shadow`, `border` from CSS declarations on card/button/nav selectors. 2. Extract button `padding` values for proportion analysis. |

### Handling difficult extraction scenarios

**CSS-in-JS (Styled Components, Emotion, CSS Modules):**
Styles are injected as `<style>` tags with hashed class names (e.g., `.sc-aBcDe`). They ARE accessible in the fetched HTML if the page is server-rendered (Next.js, Remix, Gatsby SSR). If the site is fully client-rendered, the `<style>` tags won't be in the fetched HTML. Strategy:
1. Fetch the page — check if `<style>` tags contain useful CSS
2. If yes: parse the CSS for `font-family`, color, `padding` values (ignore class names)
3. If no (client-rendered): search for the site's public Storybook, design system documentation, or Figma files
4. Many companies using CSS-in-JS publish design tokens separately (e.g., on GitHub, in documentation)
5. Last resort: extract from gallery screenshots and descriptions — fonts and colors are usually identifiable
6. Mark extraction as `[PARTIAL — visual inference]`

**Obfuscated/minified CSS:**
Minified CSS is still valid CSS — the values are readable even if selectors are hashed. Use the same extraction approach: search for `font-family`, color hex values, `padding`/`margin` values within the minified CSS.

**Cloudflare/bot protection blocks:**
1. Try `web_fetch` with standard headers
2. If 403/challenge: check Wayback Machine (`web.archive.org/web/[URL]`)
3. Check the gallery listing page — Awwwards SOTD pages typically show: screenshot, fonts used, color palette, tech stack
4. Search: `"[site name] design system"`, `"[site name] brand guidelines"`, `"[site name] fonts"`
5. Search: `"[site name]" site:fonts.google.com` (sometimes Google Fonts pages reference notable users)
6. If all fail: mark as `[GALLERY]` and extract from the gallery page description

**SPA with client-side rendering (React/Vue/Angular without SSR):**
The fetched HTML contains only `<div id="root">` or similar. However:
1. CSS files linked in `<head>` may still be fetchable (often at `/static/css/main.[hash].css`)
2. Check for `manifest.json` or `asset-manifest.json` which lists CSS file paths
3. Search for the site's public design system or Storybook
4. If on Awwwards, the SOTD page often lists tech stack and fonts
5. Mark as `[PARTIAL]` if only CSS files were extractable

**Proprietary/paid fonts (not on Google Fonts):**
1. Identify the exact font name from `font-family` declarations
2. Search for the closest Google Fonts equivalent: match x-height, weight range, and character (geometric vs humanist, etc.)
3. Document both: "Reference uses [Proprietary Font], substituted with [Google Font Alternative] for free availability"
4. Common substitutions: Helvetica Neue → Inter; Futura → Poppins or Nunito Sans; Avenir → Nunito; Proxima Nova → Montserrat; Circular → Plus Jakarta Sans

### Design system document template (`design-system.md`)

```markdown
# Design System — [Project Name]

**Derived from:** [Reference 1 URL], [Reference 2 URL], [Reference 3 URL]
**Mode:** [Multi-reference / Single-reference]
**Generated by:** design-system-tool
**Date:** [YYYY-MM-DD]

---

## Typography [REFERENCE-DERIVED]

| Role | Font | Weights | Source | Method |
|------|------|---------|--------|--------|
| Display | [family] | [weights] | [Reference X] | [PARSED] |
| Body | [family] | [weights] | [Reference X] | [PARSED] |

**Scale:**
| Element | Desktop | Mobile | Method |
|---------|---------|--------|--------|
| Hero headline | [N]px | [N]px | [PARSED/INFERRED] |
| Section title | [N]px | [N]px | [PARSED/INFERRED] |
| Subtitle | [N]px | [N]px | [PARSED/INFERRED] |
| Body | [N]px | [N]px | [PARSED/INFERRED] |
| Label/Overline | [N]px | [N]px | [PARSED/INFERRED] |

**Line-heights:** Hero [N] / Body [N] / Small [N]
**Letter-spacing:** Display [-0.0Nem] / Body [normal] / Overline [0.1em]

---

## Colors [REFERENCE-DERIVED]

| Token | Value | Role | Source | Method |
|-------|-------|------|--------|--------|
| Base | [hex] | Canvas background | [Reference X] | [PARSED] |
| Elevated | [hex] | Cards, elevated sections | [Reference X] | [PARSED] |
| Interactive | [hex] | Hover state backgrounds | [Reference X] | [INFERRED] |
| Accent primary | [hex] | CTAs, key highlights | [Reference X] | [PARSED] |
| Accent hover | [hex] | Lighter/brighter accent variant | [Reference X] | [INFERRED] |
| Accent subtle | rgba([r],[g],[b], 0.08) | Accent backgrounds/borders | Derived | [INFERRED] |
| Text primary | [hex] | Main readable text | [Reference X] | [PARSED] |
| Text secondary | [hex] | Descriptions, supporting text | [Reference X] | [PARSED] |
| Text tertiary | [hex] | Labels, captions, metadata | [Reference X] | [PARSED] |
| Border subtle | [hex] | Structural borders | [Reference X] | [PARSED] |
| Border active | [hex] | Focus/hover borders | [Reference X] | [INFERRED] |

**Theme:** [dark / light]
**Contrast check:** All text/background pairs pass WCAG AA (4.5:1 body, 3:1 large)

---

## Spacing [REFERENCE-DERIVED or DEFAULT]

- **Base unit:** [N]px — [INFERRED from recurring values]
- **Scale:** [list of values derived from base]
- **Section padding tiers:**
  - Tier 1 (breathing): [N]px — Hero, spacious sections
  - Tier 2 (standard): [N]px — Content sections
  - Tier 3 (compact): [N]px — Transition sections, footer
- **Container max-width:** [N]px — [PARSED]
- **Card padding:** [N]px — [PARSED]

---

## Imagery [REFERENCE-DERIVED]

- **Approach:** [typographic-only / product photography / custom illustrations / mixed] — [PARSED/GALLERY]
- **Photography style:** [description if applicable] — [GALLERY]
- **Icon style:** [line / filled / custom — size and color approach] — [PARSED/GALLERY]
- **Hero pattern:** [centered typographic / split with imagery / full-width visual] — [PARSED]

---

## Interactions [REFERENCE-DERIVED or DEFAULT]

- **Libraries detected:** [GSAP / Framer Motion / Lenis / Three.js / none] — [PARSED]
- **Hover philosophy:** [dimensional / flat / minimal] — [INFERRED]
  - Cards: [description] — [INFERRED from :hover CSS]
  - Buttons: [description] — [INFERRED from :hover CSS]
  - Links: [description] — [INFERRED from :hover CSS]
- **Scroll behavior:** [parallax / scroll reveals / static / scrubbed animations] — [INFERRED from library presence]
- **Animation style:** [spring / ease-out / linear] — [INFERRED from easing values in CSS]
- **Cursor:** [default / custom / none observed] — [INFERRED]
- **Immersion level recommendation:** [Subtle / Cinematic / Maximal] — [INFERRED from overall motion density]

---

## Layout [REFERENCE-DERIVED]

- **Hero:** [centered / split / full-width visual] — [PARSED]
- **Container widths:** hero [N]px, content [N]px, narrow [N]px — [PARSED]
- **Grid:** [description of observed grid/column system] — [PARSED/INFERRED]
- **Section rhythm:** [description — e.g., "alternates dark/light backgrounds, padding varies 80-140px"] — [INFERRED]
- **Footer:** [minimal / structured multi-row] — [PARSED]

---

## Anti-Pattern Exceptions

These anti-patterns from `front-end-craft/references/anti-patterns.md` are
OVERRIDDEN by this design system, with justification from real references:

| Anti-pattern | Status | Justification | Method |
|-------------|--------|---------------|--------|
| [e.g., "Inter as display font"] | ALLOWED | [Reference X] uses Inter as the primary display font — it's an intentional brand choice | [PARSED] |
| [e.g., "Pure #FFFFFF background"] | ALLOWED | [Reference X] and [Reference Y] both use pure white — it's a deliberate minimal aesthetic | [PARSED] |
| [e.g., "3-column card grid with icons"] | NOT OVERRIDDEN | No reference uses this pattern — anti-pattern remains active | — |

---

## Extraction Notes

- [Reference 1]: [completeness note — COMPLETE or PARTIAL with details]
- [Reference 2]: [completeness note]
- [Reference 3]: [completeness note]
- **Conflicts resolved:** [list of conflicts and how they were resolved]
```

### Design system CSS template (`design-system.css`)

```css
/* Design System — [Project Name] */
/* Generated by design-system-tool from: [Reference URLs] */
/* Date: [YYYY-MM-DD] */

:root {
  /* ── Typography ── */
  --font-display: '[Display Font]', [fallback-stack];
  --font-body: '[Body Font]', [fallback-stack];

  --text-hero: [N]px;
  --text-h2: [N]px;
  --text-h3: [N]px;
  --text-body: [N]px;
  --text-small: [N]px;
  --text-overline: [N]px;

  --leading-hero: [N];
  --leading-body: [N];
  --leading-small: [N];

  --tracking-display: -0.02em;
  --tracking-body: normal;
  --tracking-overline: 0.1em;

  /* ── Colors ── */
  --color-base: [hex];
  --color-elevated: [hex];
  --color-interactive: [hex];

  --color-accent: [hex];
  --color-accent-hover: [hex];
  --color-accent-subtle: rgba([r], [g], [b], 0.08);

  --color-text-primary: [hex];
  --color-text-secondary: [hex];
  --color-text-tertiary: [hex];

  --color-border: [hex];
  --color-border-active: [hex];

  /* ── Spacing ── */
  --space-unit: [N]px;
  --space-1: [1×unit]px;
  --space-2: [2×unit]px;
  --space-3: [3×unit]px;
  --space-4: [4×unit]px;
  --space-6: [6×unit]px;
  --space-8: [8×unit]px;
  --space-12: [12×unit]px;
  --space-16: [16×unit]px;
  --space-20: [20×unit]px;

  --section-padding-lg: [N]px;
  --section-padding-md: [N]px;
  --section-padding-sm: [N]px;

  /* ── Layout ── */
  --container-max: [N]px;
  --container-narrow: [N]px;
  --container-hero: [N]px;

  /* ── Components ── */
  --radius-card: [N]px;
  --radius-button: [N]px;
  --shadow-card: [value];
  --shadow-button: [value];
}
```

### Consolidation rules (multiple references → one system)

When multiple references provide conflicting values for the same token:

1. **Typography:** If 2+ references share the same display font → use it. If all differ → choose the one that best matches the stated tone (luxury=serif display, tech=geometric sans, playful=rounded). Body font: prioritize readability — choose the most legible option.
2. **Colors:** Use the palette from the reference whose overall tone most closely matches the target brand. Secondary references contribute accent variations. If user stated a preference ("I like the dark theme of Site A"), that reference gets priority for colors.
3. **Spacing:** Use the base unit from the reference with the most consistent spacing system. If all use 8px → use 8px. If one uses 4px and another 8px → use 8px (safer default).
4. **Motion:** Match the overall motion philosophy of the majority. If 2/3 references use subtle hovers → the design system is subtle. One outlier with heavy animation doesn't override.
5. **Layout:** Hero layout from the reference closest to the project's industry. Container widths from the reference with the most professional spacing.
6. **Document everything:** Every conflict and its resolution goes in `references-report.md` with reasoning.

**Single-reference mode:** When only 1 reference was approved, skip consolidation entirely. Map tokens directly from the single extraction. Flag: "No consolidation needed — single reference."

---

## F) Integration with Build Skills

### Contract — file detection

Both build skills check for `design-system/design-system.md` at the start of their loading flow:

- **front-end-craft:** The "Prerequisite: Design System" section tells the agent to check if `design-system/design-system.md` exists. If it does → load it. If not → proceed with defaults.
- **landing-page-craft:** The updated "When This Skill Is Loaded" section checks for `design-system/design-system.md` first and recommends running `design-system-tool` if absent.

No flags, env variables, or special signals needed. **File presence at `design-system/design-system.md` is the signal.**

### Override mapping

| Design system section | Overrides in front-end-craft | Overrides in landing-page-craft |
|---|---|---|
| Typography | Step 3 font pairing table, banned fonts list, typographic scale | — |
| Colors | Step 2 color system, pure black/white soft-ban, 15% accent rule | — |
| Spacing | Step 4 8px grid base, spacing scale | Step 4 section padding tiers |
| Imagery | — | Step 4 hero layout (centered vs split), Step 5 "no stock photos" |
| Interactions | Step 5 immersion level, 3D tilt default, grain texture default | Step 4 immersive layer per section table |
| Layout | — | Step 4 layout variety table, container width guidance |
| Anti-pattern exceptions | anti-patterns.md conditional application (framing section) | Section skip rules (ALWAYS skip → skip BY DEFAULT) |

### Fallback behavior

- If design system covers typography but not spacing → front-end-craft uses its own 8px grid for spacing, design system fonts for typography
- If design system covers colors but not interactions → front-end-craft uses design system colors with its own Cinematic immersion defaults
- If design system exists but is marked `[DEFAULT — no reference data]` for a section → treat that section as if no design system exists (apply full prescriptive rules)
- If `design-system/design-system.md` doesn't exist at all → both skills operate in full prescriptive mode (all defaults active, all anti-patterns enforced)
- If a token is tagged `[INFERRED]` or `[GALLERY]` and the build skill's own default seems stronger for that specific decision → the build skill may prefer its default, documenting: "Design system suggests X [INFERRED] but using default Y for higher confidence"

---

## G) File Architecture

```
design-system-tool/
├── SKILL.md                         ← Main agent instructions: 6-step flow, loading order
├── references/
│   ├── research-sources.md          ← Gallery URLs, query patterns, scoring criteria
│   ├── extraction-guide.md          ← How to extract tokens from different tech stacks
│   └── consolidation-rules.md       ← How to merge multiple references into one system
└── templates/
    ├── design-system-doc.md         ← Template for the .md output file
    └── design-system-code.css       ← Template for the .css output file
```

### Justifications

| File | Why it exists | Why in this location |
|------|---------------|---------------------|
| **SKILL.md** | Contains the 6-step flow, loading instructions, and orchestration logic | Root of skill directory — standard Claude skill structure |
| **references/research-sources.md** | The gallery list is ~50+ lines with URLs, query patterns, scoring criteria. Too long for SKILL.md. Agent reads it during Step 2. | `references/` — knowledge the agent consults during execution |
| **references/extraction-guide.md** | Detailed technical instructions for extracting tokens from different tech stacks (CSS-in-JS, minified CSS, SPAs, Cloudflare). Includes the reliability table. Agent reads during Step 4. | `references/` — technical reference for a specific step |
| **references/consolidation-rules.md** | Rules for merging multiple reference extractions. Conflict resolution logic, priority rules, token-by-token guidance. Agent reads during Steps 5-6. | `references/` — separate from extraction because different phase, different concerns (design decisions vs technical extraction) |
| **templates/design-system-doc.md** | Literal fill-in-the-blank template that gets copied and populated. Defines the exact output format that build skills consume. Includes the Method column. | `templates/` — not a reference to read, but a structure to copy and fill. Different purpose from references. |
| **templates/design-system-code.css** | CSS custom properties template. Defines the exact variable names that build skills can import. | `templates/` — same reasoning as above. Defines the OUTPUT CONTRACT. |

---

## Content Distribution — SKILL.md vs References vs Templates

### SKILL.md (~250 lines maximum)
Contains ONLY:
- YAML frontmatter (name + description)
- Section "Position in ecosystem" summarized (diagram + dependency table)
- Output location convention (`design-system/`)
- Steps 1-6 in summarized format:
  - For each step: purpose (1 line), inputs/outputs (short table), advancement criterion (1 line), fallback (1 line with pointer to reference if fallback is complex)
  - Execution details live in the references
- How to present references (Step 3 presentation guidelines)
- Extraction honesty rule (Step 4 method tags)
- Quality checklist (Section I)
- "When This Skill Is Loaded" with step-based loading order

### references/research-sources.md (~80 lines)
Contains:
- Complete gallery table (Section D — curated sources)
- Query construction patterns (Tier 1, 2, 3)
- Acceptance and rejection criteria for references
- Quantity and diversity rules (including single-reference mode)

### references/extraction-guide.md (~150 lines)
Contains:
- Extraction reliability table (by category — HIGH/MEDIUM/LOW)
- Detailed extraction table (what to extract and how, with realistic methods)
- Handling difficult scenarios (CSS-in-JS, obfuscated CSS, Cloudflare, SPA, proprietary fonts)
- Edge cases 2, 3, 4 (blocked site, CSS-in-JS, SPA)

### references/consolidation-rules.md (~100 lines)
Contains:
- Consolidation rules by category (typography, colors, spacing, motion, layout)
- Single-reference mode instructions
- Edge cases 5, 6, 7, 8 (conflicting references, low-quality reference, no results for industry, incomplete extraction)
- Edge case 1 (user approves no references) stays in SKILL.md because it's about the flow, not consolidation

### templates/design-system-doc.md (~80 lines)
- Literal template of design-system.md (with Method column in all tables)

### templates/design-system-code.css (~50 lines)
- Literal template of CSS tokens

### Loading order rule

The SKILL.md must instruct WHEN to read each reference, not "read everything upfront":

```
## When This Skill Is Loaded

1. Read `references/research-sources.md` — needed for Step 2
   (do NOT read before Step 1 is complete)
2. Read `references/extraction-guide.md` — needed for Step 4
   (do NOT read before Step 3 is complete)
3. Read `references/consolidation-rules.md` — needed for Step 5
   (do NOT read before Step 4 is complete)
4. Templates are COPIED during output generation (Steps 5-6), not read upfront
```

---

## H) Edge Cases

### 1. User approves no references

**Scenario:** User rejects all 5-8 candidates in Step 3.
**Solution:**
1. Ask what specifically they disliked — capture structured feedback (color? layout? industry fit? too trendy? too conservative?)
2. Re-run Step 2 with adjusted queries: different galleries, different tone keywords, adjacent industries
3. Max 2 retry cycles (total 3 attempts including the original)
4. If still no approval after 3 attempts:
   - Option A: User provides their own reference URLs (proceed to Step 4 with those)
   - Option B: Proceed without design system. Explicitly flag: "The output will use default aesthetic choices — not derived from real references." Both build skills fall back to prescriptive mode.

### 2. Reference site is down or blocks extraction

**Scenario:** Approved site returns 403, timeout, or Cloudflare challenge.
**Solution:**
1. `web_fetch` with standard headers
2. Wayback Machine: `web.archive.org/web/[URL]`
3. Gallery page data: Awwwards SOTD pages show fonts, colors, tech stack, screenshots
4. Web search: `"[site name] design system"`, `"[site name] brand guidelines"`, `"[site name] fonts"`
5. If all fail: drop this reference, proceed with remaining references
6. If it was the only/last approved reference: ask user for an alternative URL
7. Document in `references-report.md`: "[URL] — blocked, extraction from gallery data only [GALLERY]"

### 3. Site uses CSS-in-JS without CSS variables

**Scenario:** Site uses Styled Components/Emotion/CSS Modules. No `:root` variables. Class names are hashed.
**Solution:**
1. Check if page is server-rendered (Next.js, Remix) — `<style>` tags will contain the actual CSS values despite hashed class names
2. Parse `<style>` tags for `font-family`, color hex values, padding values
3. If client-rendered only: search for public Storybook (`[site name] storybook`), design system docs, or GitHub repos
4. Check if the company publishes an npm package with design tokens
5. Last resort: gallery descriptions and screenshots for visual inference
6. Mark extraction as `[PARTIAL — CSS-in-JS, visual inference for: [missing categories]]`

### 4. Site is SPA with client-side rendering

**Scenario:** Fetched HTML is just `<div id="root">`. No meaningful HTML structure or styles.
**Solution:**
1. CSS files are often still linked in `<head>` even for SPAs — try fetching them directly
2. Check for `asset-manifest.json` or `manifest.json` listing CSS file paths
3. Try common paths: `/static/css/main.[hash].css`, `/assets/index-[hash].css`
4. Search for the site's public design system or Storybook
5. If on Awwwards: the SOTD page often lists tech stack, fonts, and shows screenshots
6. Same ultimate fallback as #3: gallery inference, marked `[GALLERY]`

### 5. Approved references are aesthetically conflicting

**Scenario:** User approves a dark minimal site AND a colorful maximalist site.
**Solution:**
1. In Step 5, present the conflict explicitly: "Reference A and Reference B have conflicting aesthetics. A is dark/minimal, B is colorful/maximal."
2. Ask user: "Which direction fits your brand better? I'll use it as the primary reference."
3. Use the chosen primary for the overall aesthetic (colors, mood, motion intensity)
4. Cherry-pick specific elements from the secondary: "Typography from A, accent color approach from B"
5. Document all cherry-picking decisions in `references-report.md`

### 6. User wants a reference that is low quality

**Scenario:** User provides a URL that fails quality criteria (template site, dated design, poor accessibility).
**Solution:**
1. Flag specific concerns honestly: "This site appears to use a [Wix/ThemeForest] template" or "The design has some accessibility issues (low contrast text, missing hover states)"
2. Recommend: "Want to proceed anyway, or add a higher-quality reference as the primary so the design system draws mainly from that?"
3. If user insists: extract anyway, but weight higher-quality approved references more heavily in consolidation
4. Document in `references-report.md`: "[URL] — user-provided, quality concerns: [list]. Weighted lower in consolidation."

### 7. Research returns no results for the industry

**Scenario:** Searching for "pet grooming" or "agricultural equipment" on design galleries yields 0-2 results.
**Solution:**
1. Expand to adjacent industries in concentric circles:
   - Pet grooming → pet services → veterinary → pet products → local services → wellness
   - Agricultural equipment → industrial products → B2B equipment → manufacturing → industrial SaaS
2. If adjacent industries also fail: search for general pages matching the TONE rather than industry:
   - "best minimal dark landing page 2025"
   - "best luxury website design"
3. Flag to user: "Your industry has limited representation in design galleries. I've found references from [adjacent industry] that match your desired tone. They'll need more adaptation than same-industry references."

### 8. Extracted design system is incomplete

**Scenario:** Typography and colors extracted successfully, but spacing, motion, and imagery data are missing from all references.
**Solution:**
1. Fill missing categories with front-end-craft defaults:
   - Spacing: 8px grid (4, 8, 12, 16, 24, 32, 48, 64, 80, 96, 120, 160px)
   - Motion: Cinematic level (default for most projects)
   - Imagery: typographic-only (safest default when no imagery data)
2. Mark each section clearly in `design-system.md`:
   - `## Typography [REFERENCE-DERIVED]`
   - `## Colors [REFERENCE-DERIVED]`
   - `## Spacing [DEFAULT — no reference data]`
   - `## Interactions [DEFAULT — no reference data]`
3. Build skills handle this gracefully — they check each section's tag and apply design system values where `[REFERENCE-DERIVED]`, own defaults where `[DEFAULT]`
4. Flag to user: "The design system is partially derived from references. [Spacing/Motion/Imagery] are using defaults because the reference sites didn't provide enough extractable data for these categories."

---

## I) Quality Checklist

### Design system completeness
- [ ] Design system includes all 7 sections (typography, colors, spacing, imagery, interactions, layout, anti-pattern exceptions)
- [ ] Each section is tagged `[REFERENCE-DERIVED]` or `[DEFAULT — no reference data]`
- [ ] Every reference-derived token has a Method tag (`[PARSED]`, `[INFERRED]`, or `[GALLERY]`)
- [ ] Every reference-derived token traces back to a specific reference URL

### Typography
- [ ] Display + body fonts identified with specific weights
- [ ] Font choices are available on Google Fonts (or closest substitutes documented with reasoning)
- [ ] Typographic scale covers: hero, h2, body, small, overline (desktop + mobile)

### Colors
- [ ] Full palette defined with roles (base, elevated, accent, text hierarchy, borders)
- [ ] All text/background color pairs pass WCAG AA contrast (4.5:1 body text, 3:1 large text)
- [ ] Contrast failures from references are auto-corrected and documented

### References
- [ ] At least 1 reference site analyzed (2+ preferred, 3+ ideal)
- [ ] User approved the reference selection (Step 3 gate passed)
- [ ] No more than 2 references share near-identical aesthetics

### Consolidation
- [ ] Every conflict between references is documented with resolution reasoning
- [ ] Anti-pattern exceptions have explicit justification citing specific reference URLs
- [ ] Cherry-picked elements (e.g., "typography from A, colors from B") are documented

### Output files
- [ ] `design-system.md` follows the template format exactly (including Method column)
- [ ] `design-system.css` contains valid CSS custom properties with no syntax errors
- [ ] `references-report.md` includes all analyzed references with extraction notes
- [ ] All files written to `design-system/` directory

### Integration
- [ ] Both build skills can consume the output without manual interpretation
- [ ] Incomplete sections are clearly marked so build skills know to use defaults
- [ ] Files exist at `design-system/design-system.md` for build skill detection

---

## Changelog — Corrections Applied

| # | Correction | Where it appears |
|---|-----------|-----------------|
| 1 | **Extraction realism** — Added reliability table (HIGH/MEDIUM/LOW by category), replaced "computed styles" and "interact with the site" with realistic `web_fetch`-based methods, added extraction honesty rule (`[PARSED]`/`[INFERRED]`/`[GALLERY]` tags), added Method column to design-system.md template | Section E (reliability table, extraction table, all scenario handlers), Section C Step 4 (honesty rule, per-site template), design-system.md template (all token tables), Section I checklist |
| 2 | **Pragmatic research queries** — Replaced `site:` operator patterns with tiered query strategy (Tier 1 reliable, Tier 2 refinement, Tier 3 fallback), demoted `site:` to "alternative that may work" | Section D (query construction patterns) |
| 3 | **Reference presentation** — Added "How to present references" guidelines: specific design descriptions instead of generics, instruct user to open links, highlight what's worth extracting per reference | Section C Step 3 |
| 4 | **Flexible minimum references** — Changed minimum from 3 to standard 2 (preferred 3) with single-reference mode when user explicitly chooses one perfect reference | Section D (quantity rules), Section E (consolidation rules — single-reference mode), Section C Step 3 (advance criterion), Section I checklist |
| 5 | **Content distribution** — Added "Content Distribution" section mapping exactly what goes in SKILL.md (~250 lines) vs each reference file vs templates, with step-based loading order instead of "read everything upfront" | New section between G and H |
| 6 | **Fixed output path** — Defined `design-system/` as the contract path for all outputs, updated all references: flow diagram, outputs table, Step 5/6 outputs, file detection in Section F, quality checklist | Section A (diagram + output convention), Section B (outputs table), Section C Steps 5-6, Section F (file detection + fallback), Section I checklist |
