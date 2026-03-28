# Implementation Details — The Code-Level Decisions AI Gets Wrong

This file covers the implementation-level details that anti-patterns.md and section-blueprints.md don't address. These are the "last mile" decisions — spacing, mobile behavior, accessibility, button proportions, Tailwind patterns — where AI-generated code silently degrades from "professional" to "close but off."

Read this file after reading anti-patterns.md and before writing code.

## Table of Contents
1. [Spacing System](#spacing)
2. [Text Spacing Relationships](#text-spacing)
3. [Light Theme Architecture](#light-theme)
4. [Mobile Design (Not Just Responsive)](#mobile)
5. [Button and CTA Precision](#buttons)
6. [Accessibility Baseline](#accessibility)
7. [Tailwind Implementation Patterns](#tailwind)
8. [Font Loading Strategy](#font-loading)
9. [Section Transitions](#section-transitions)
10. [Using Real Photography](#photography)

---

<a id="spacing"></a>
## 1. Spacing System

AI uses arbitrary spacing — `padding: 24px` here, `margin: 32px` there, `gap: 20px` somewhere else — without a coherent scale. The page looks "almost right" but subtly amateur, like a musician playing slightly out of tune.

### The 8px grid

All spacing values should come from an 8px scale: 4, 8, 12, 16, 24, 32, 48, 64, 80, 96, 120, 160px. Never use odd values like 15px, 18px, 30px, 50px, or 100px (except for font sizes, which follow their own scale).

```
SPACING SCALE:
  4px     →  micro gaps (icon-to-text, badge padding)
  8px     →  tight internal gaps (between related list items)
  12px    →  compact spacing (within a card, between label and input)
  16px    →  standard internal spacing (padding inside small cards, between paragraphs)
  24px    →  comfortable padding (card padding, gap between grouped elements)
  32px    →  section-internal separation (between a title and its content block)
  48px    →  major internal break (between content groups within a section)
  64px    →  small section padding (compact sections like credibility bar)
  80px    →  medium section padding (content sections on mobile)
  96px    →  standard section padding (most content sections desktop)
  120px   →  generous section padding (hero, final CTA, sections that breathe)
  160px   →  dramatic breathing room (only on the most spacious sections)
```

### Proximity principle — the most common AI mistake

Related elements need TIGHTER spacing than unrelated ones. AI tends to use uniform gaps everywhere, which breaks visual grouping.

```
WRONG (uniform gaps):
  Section Title          ← 32px gap
  Section Subtitle       ← 32px gap
  Content Block          ← 32px gap
  CTA Button             ← 32px gap

RIGHT (proximity-based):
  Section Title          ← 12-16px gap (title and subtitle are a unit)
  Section Subtitle       ← 32-48px gap (bigger gap = new visual group)
  Content Block          ← 32-48px gap
  CTA Button
```

The title-to-subtitle gap should be roughly HALF the subtitle-to-content gap. This visually binds the title and subtitle together while separating them from the content below.

### Common spacing relationships

```
Overline → Title:           8-12px   (very tight, they're one unit)
Title → Subtitle:           12-16px  (tight, still the same header group)
Subtitle → Content:         32-48px  (clear break, new visual group)
Content → CTA:              32-48px  (clear break before the action)
CTA → Microcopy:            8-12px   (tight, microcopy supports the CTA)

Inside cards:
  Icon → Title:             16px
  Title → Description:      8-12px
  Card padding:             24-32px

Between cards:
  Gap in grid:              16-24px

Section padding (vertical):
  Impact sections:          120-160px
  Content sections:         96-120px
  Compact sections:         48-64px
```

### The section padding anti-pattern

Never use the same vertical padding for all sections. This is one of the most visible tells of AI-generated pages. Define 3 tiers:

```
TIER 1 (BREATHING):  120-160px  →  Hero, Final CTA, sections meant to feel expansive
TIER 2 (STANDARD):   80-96px   →  How It Works, Why Us, FAQ
TIER 3 (COMPACT):    48-64px   →  Credibility bar, transition sections, footer
```

Alternate tiers: breathing → compact → standard → breathing. Never stack two identical-padding sections.

---

<a id="text-spacing"></a>
## 2. Text Spacing Relationships

The gap between headline, subtitle, body, and CTA is where professional design separates from amateur work. AI gets this wrong in predictable ways.

### The hero stack

```
                                    SPACING
  [Overline]                        
         ↕  8-12px                  ← very tight: overline introduces the headline
  [Headline]                        
         ↕  16-20px                 ← tight: subtitle completes the headline's thought
  [Subheadline]                     
         ↕  32-40px                 ← wide: visual break before the action
  [CTA Button]                      
         ↕  8-12px                  ← very tight: microcopy is part of the CTA
  [Microcopy]                       
```

The pattern: related elements cluster tight, unrelated elements separate. The headline-to-subtitle distance should feel like the subtitle is "attached" to the headline. The subtitle-to-CTA distance should feel like a deliberate pause.

### Section header stack

```
  [Overline]
         ↕  8px
  [Section Title]
         ↕  12-16px
  [Section Subtitle]    (if present)
         ↕  48-64px     ← dramatic gap before content starts
  [Content]
```

The gap between the section header group and the section content is the most important spacing decision in each section. Make it generous — 48px minimum. This gap is what separates "title then content" from "title floating above content."

### Line-height relationships

```
Headlines (display font):     line-height: 1.05-1.15  (tight, impactful)
Subtitles:                    line-height: 1.4-1.5    (moderate)
Body text:                    line-height: 1.6-1.8    (generous, readable)
Labels/Overlines:             line-height: 1.2-1.3    (compact)
```

Headlines should feel dense and bold. Body text should feel open and readable. If both have the same line-height, the hierarchy flattens.

---

<a id="light-theme"></a>
## 3. Light Theme Architecture

The default guidance in this skill leans dark (near-black backgrounds, light text). Many brands — especially in health, education, finance, food, children, and most B2B SaaS — need light themes. Here's how to adapt the entire system for light.

### Light palette architecture

```
BACKGROUNDS (depth hierarchy — lighter = further back):
  Base:             #FAFAFA or #F8F8F8    (warm off-white, NEVER pure #FFFFFF)
  Elevated:         #FFFFFF               (white cards on off-white base = subtle depth)
  Interactive:      #F0F0F0               (hover state)
  Surface:          #F5F5F5               (modals, dropdowns)

TEXT (never pure #000000):
  Primary:          #1A1A1A or #111111    (near-black, warm)
  Secondary:        #555555 or #6B7280    (mid-gray)
  Tertiary:         #999999 or #9CA3AF    (captions, labels)

BORDERS:
  Subtle:           rgba(0, 0, 0, 0.06)  (barely visible structure)
  Standard:         rgba(0, 0, 0, 0.1)   (visible separation)
  Active:           [accent-tinted with 30% opacity]

ACCENT:
  Same rules as dark theme — ≤15%, used for CTAs and key highlights.
  On light backgrounds, accent colors may need to be slightly darker/more saturated
  to maintain contrast. Test your accent on white — if it's hard to read, darken it.
```

### Light theme pitfalls AI creates

**The washed-out page**: AI uses light grays for everything — #F5F5F5 background, #E5E5E5 cards, #CCCCCC borders. Nothing has contrast. The page looks like it was printed on a foggy day.

**Fix**: Use actual white (#FFFFFF) for elevated elements (cards) on an off-white base (#FAFAFA). The contrast between "white card on slightly gray background" creates clear depth without needing shadows.

**The shadow addiction**: To compensate for low color contrast, AI adds box-shadows to everything. The page gets littered with `shadow-md` and `shadow-lg`.

**Fix**: Shadows should be subtle and selective. Cards can have `shadow-sm` (or just a border). Only one element per viewport (modals, dropdowns) should have a prominent shadow. Use background color differences for depth, not shadows.

**The invisible accent**: An accent color that worked beautifully on dark backgrounds (e.g., #E2B84B gold) may look washed-out or low-contrast on white. 

**Fix**: Test accent color on white background for WCAG contrast. You may need a darker variant: #C49A30 instead of #E2B84B, or #2563EB instead of #60A5FA. The accent still follows the 15% rule.

**The gray-on-gray text**: Body text in #999 on a #F5F5F5 background. Technically visible, but straining to read.

**Fix**: Body text must pass WCAG AA contrast ratio (4.5:1 for normal text). On #FAFAFA background:
  - Primary text: #1A1A1A minimum (ratio ~16:1)
  - Secondary text: #555555 minimum (ratio ~7:1)
  - Tertiary text: #777777 minimum (ratio ~4.5:1)

### Light theme section alternation

On dark themes, sections alternate between #09090B and #131316 (nearly invisible difference). On light themes, the alternation is more visible:

```
Hero:              #FAFAFA (base)
Credibility bar:   #FFFFFF (elevated) with top/bottom borders
How It Works:      #FAFAFA (base)
Why Us:            #FFFFFF (elevated) or #F0F4F8 (very light tint of brand color)
Numbers:           #FAFAFA (base)
FAQ:               #FFFFFF (elevated)
Final CTA:         #FAFAFA (base) or very subtle gradient
Footer:            #111111 or #1A1A1A (dark footer on light page creates grounding)
```

A dark footer on a light page is a classic editorial move — it "closes" the page with visual finality.

---

<a id="mobile"></a>
## 4. Mobile Design (Not Just Responsive)

"It doesn't break on 375px" is not mobile design. Mobile is a fundamentally different context: smaller viewport, touch interaction, thumb-driven navigation, reduced attention span, portrait orientation. The page needs to be redesigned for mobile, not just reflowed.

### Font size adaptation

Don't just scale desktop sizes down uniformly. The RATIOS between sizes change on mobile:

```
DESKTOP → MOBILE ADAPTATION:

  Hero headline:     56-72px → 36-44px       (reduce ~40%)
  Section titles:    36-44px → 26-32px       (reduce ~30%)
  Subtitles:         18-20px → 16-18px       (reduce ~15%)
  Body text:         16-18px → 15-16px       (barely change)
  Labels/Overlines:  12-13px → 11-12px       (barely change)
  CTA buttons:       16px → 16px             (DON'T reduce — tappability)
```

The key insight: headlines compress dramatically, but body text barely changes. On mobile, the hierarchy gap between headline and body shrinks — this is intentional. Mobile has less vertical space, so headlines don't need to be as dramatically large to dominate.

### Mobile-specific layout rules

**CTAs become full-width**: On screens < 768px, CTA buttons should be `width: 100%` (within padding). A narrow centered button on mobile has a small tap target and looks lost.

**Grids collapse to single column**: The asymmetric 60/40 grid becomes a stack. But DON'T just stack everything — reconsider what to show first. On desktop, a side-by-side layout lets both elements be scanned simultaneously. On mobile, the first element sets context for the second.

**The navbar becomes a hamburger**: But the CTA button stays OUTSIDE the hamburger menu, visible at all times. A compact CTA in the navbar that's always visible converts better than one buried inside the mobile menu.

**Padding reduces but not linearly**:
```
Desktop section padding 120px → Mobile 64-80px  (NOT 120px on mobile — wastes screen)
Desktop card padding 32px → Mobile 20-24px
Desktop container margin 10vw → Mobile 20-24px
Desktop gap between cards 24px → Mobile 16px
```

**The floating CTA adjusts**:
```
Desktop: 56px button, bottom 24px, right 24px
Mobile:  48px button, bottom 16px, right 16px
```

### Touch targets

Minimum tap target: 44×44px (Apple HIG) or 48×48px (Material). This applies to:
- Nav links in the mobile menu
- FAQ accordion triggers
- Social icons in footer
- The hamburger button itself
- Any link or clickable element

AI frequently creates 32px touch targets that are frustrating to tap. When in doubt, add padding to increase the tappable area without changing the visual size.

### What to REMOVE on mobile

Not everything on the desktop page should appear on mobile. Consider hiding:
- Scroll indicator arrow in the hero (mobile users naturally scroll)
- Decorative separator lines between stats (use spacing instead)
- Long subtitles (shorter subtitle or skip entirely)
- Hover-dependent tooltip text (hover doesn't exist on touch)

### The mobile hero trap

AI often makes the mobile hero too tall — keeping 100vh but with large text and multiple elements, pushing the CTA below the fold. On mobile, the visitor needs to see the headline AND the CTA without scrolling.

**Fix**: On mobile, the hero should be `min-height: 100svh` (not `100vh` — use `svh` to account for mobile browser chrome). But if the content doesn't fill 100svh comfortably, let it be shorter. The priority is: headline + CTA visible without scrolling. The subheadline and overline can scroll below the fold — they're supplementary.

---

<a id="buttons"></a>
## 5. Button and CTA Precision

The CTA button is the single most important element on the page. AI gets the proportions wrong in subtle ways that reduce click-through.

### Button anatomy

```
┌──────────────────────────────────────┐
│    [icon 20px]  Button Text  16px    │  ← horizontal: 32-40px padding each side
│                                      │  ← vertical: 14-18px padding top/bottom
└──────────────────────────────────────┘
  ↑ border-radius: 8px (max 12px)
  ↑ icon-to-text gap: 8-10px
```

### The proportion rules

**Height-to-width ratio**: A primary CTA button should feel "landscape" — wider than it is tall. The sweet spot is roughly 3:1 to 5:1 width-to-height. A button that's 40px tall and 200px wide feels intentional. A button that's 50px tall and 100px wide feels like a fat square.

**Padding ratio**: Horizontal padding should be 2-2.5× vertical padding. If vertical padding is 16px, horizontal should be 32-40px. This creates the "wide and confident" feel.

**Font size**: 15-17px for primary CTAs. Never smaller than 14px (hard to read) or larger than 20px (looks like a headline pretending to be a button).

**Font weight**: 500-600. Never 400 (looks timid) or 700+ (looks like it's shouting).

### AI button mistakes

**Too rounded**: `border-radius: 9999px` (full pill) looks playful/casual. For most brands, 8px radius is professional. Use pill shape only for very casual or mobile-app-style brands.

**Too many effects**: AI stacks gradient + shadow + border + glow on one button. Pick ONE enhancement: either a subtle shadow OR a subtle glow OR a border. Never combine multiple.

**Same size everywhere**: The hero CTA, the mid-page CTA, and the nav CTA should NOT be identical. Scale: hero CTA is largest (padding 16px 40px), mid-page CTA is standard (14px 32px), nav CTA is compact (10px 24px).

**No visual weight difference**: Primary and secondary buttons should be visually distinct. Primary: solid accent background. Secondary: transparent with border. If they look similar, the visitor doesn't know which to click.

### CTA with icon

When the CTA includes an icon (e.g., WhatsApp), the icon goes LEFT of the text. Size: 20px. Gap: 8-10px. The icon should be the same color as the text (not a separate color).

```
✅  [WhatsApp icon]  Falar pelo WhatsApp
❌  Falar pelo WhatsApp  [WhatsApp icon]    ← icon after text is unusual and confusing
❌  [BIG 32px icon]  Falar pelo WhatsApp    ← icon too large, competes with text
```

---

<a id="accessibility"></a>
## 6. Accessibility Baseline

A page that looks professional but can't be navigated by keyboard or read by a screen reader is incomplete work. These are the minimum accessibility requirements — not optional extras.

### Contrast ratios (WCAG AA)

```
Normal text (< 24px):    4.5:1 minimum contrast ratio
Large text (≥ 24px):     3:1 minimum contrast ratio
Interactive elements:    3:1 minimum against adjacent colors

TOOLS: Use a contrast checker before finalizing any text/background pair.
       https://webaim.org/resources/contrastchecker/
```

**Common failures on dark themes**:
- #71717A on #09090B → ratio ~4.2:1 — FAILS for body text. Use #8B8B95 minimum.
- #A1A1AA on #131316 → ratio ~6.5:1 — passes.
- Accent color as text on dark bg — always check. Gold (#E2B84B) on #09090B → ~9:1 — passes.

**Common failures on light themes**:
- #999999 on #FFFFFF → ratio ~2.8:1 — FAILS. Use #767676 minimum.
- Light accent colors on white → almost always fail. Darken the accent.

### Focus states

Every interactive element (link, button, accordion trigger, nav item) must have a visible focus state for keyboard navigation:

```css
:focus-visible {
  outline: 2px solid [accent-color];
  outline-offset: 2px;
}
```

Use `:focus-visible` (not `:focus`) to show focus rings only on keyboard navigation, not on mouse clicks.

### Semantic HTML

```
<nav>              ← navbar
<main>             ← page content
<section>          ← each content section, with aria-label or aria-labelledby
<h1>               ← hero headline (ONE per page)
<h2>               ← section titles
<h3>               ← sub-sections within a section
<button>           ← clickable elements that don't navigate (accordion triggers, menu toggle)
<a>                ← elements that navigate (CTA links, nav links, footer links)
<footer>           ← footer
```

Heading hierarchy must be sequential: h1 → h2 → h3. Never skip from h1 to h3, and never use headings for visual styling (use CSS classes instead).

### Skip link

Add a visually hidden "Skip to content" link as the first focusable element:

```html
<a href="#main-content" class="sr-only focus:not-sr-only focus:absolute focus:top-4 focus:left-4 focus:z-[9999] focus:bg-[accent] focus:text-[bg] focus:px-4 focus:py-2 focus:rounded">
  Skip to content
</a>
```

### Reduced motion

Respect users who prefer reduced motion:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

---

<a id="tailwind"></a>
## 7. Tailwind Implementation Patterns

AI using Tailwind makes predictable mistakes. Follow these patterns to keep the code professional and maintainable.

### CSS custom properties first, Tailwind utilities second

Define the entire design system as CSS custom properties, then reference them in Tailwind. Never hardcode hex values in class names.

```css
/* globals.css or app.css */
:root {
  --color-bg-base: #09090B;
  --color-bg-elevated: #131316;
  --color-bg-interactive: #1C1C21;
  
  --color-accent: #E2B84B;
  --color-accent-hover: #ECC96A;
  --color-accent-subtle: rgba(226, 184, 75, 0.08);
  
  --color-text-primary: #F4F4F5;
  --color-text-secondary: #A1A1AA;
  --color-text-tertiary: #71717A;
  
  --color-border-subtle: rgba(255, 255, 255, 0.06);
  --color-border-active: rgba(226, 184, 75, 0.3);
  
  --font-display: 'Playfair Display', Georgia, serif;
  --font-body: 'Plus Jakarta Sans', system-ui, sans-serif;
  
  --radius-sm: 8px;
  --radius-md: 12px;
  --radius-lg: 16px;
}
```

Then in Tailwind v4, these are automatically available:

```html
<!-- RIGHT: references design system -->
<h1 class="font-[--font-display] text-[--color-text-primary]">Title</h1>
<div class="bg-[--color-bg-elevated] border border-[--color-border-subtle] rounded-[--radius-md]">

<!-- WRONG: hardcoded values scattered everywhere -->
<h1 class="font-serif text-[#F4F4F5]">Title</h1>
<div class="bg-[#131316] border border-[rgba(255,255,255,0.06)] rounded-xl">
```

For Tailwind v3, extend the theme in `tailwind.config.js`:

```js
theme: {
  extend: {
    colors: {
      'bg-base': 'var(--color-bg-base)',
      'bg-elevated': 'var(--color-bg-elevated)',
      'accent': 'var(--color-accent)',
      // ...
    },
    fontFamily: {
      'display': 'var(--font-display)',
      'body': 'var(--font-body)',
    },
  },
}
```

### Component structure

One component per section. Name them descriptively:

```
src/
  components/
    Navbar.tsx
    Hero.tsx
    CredibilityBar.tsx
    ProcessSteps.tsx
    Arguments.tsx
    SocialProof.tsx
    FAQ.tsx
    FinalCTA.tsx
    Footer.tsx
    FloatingWhatsApp.tsx
  hooks/
    useInView.ts
    useScrollPosition.ts
  App.tsx
```

### The useInView hook

```tsx
import { useRef, useState, useEffect } from 'react';

export function useInView(options: IntersectionObserverInit = {}) {
  const ref = useRef<HTMLElement>(null);
  const [isInView, setIsInView] = useState(false);

  useEffect(() => {
    const element = ref.current;
    if (!element) return;

    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsInView(true);
          observer.disconnect();
        }
      },
      { threshold: 0.15, ...options }
    );

    observer.observe(element);
    return () => observer.disconnect();
  }, []);

  return { ref, isInView };
}
```

Usage:

```tsx
function ProcessSteps() {
  const { ref, isInView } = useInView();
  
  return (
    <section ref={ref}>
      <div className={`transition-all duration-500 ${isInView ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-6'}`}>
        {/* content */}
      </div>
    </section>
  );
}
```

### Responsive class ordering

Keep responsive classes in a consistent order: base → sm → md → lg → xl.

```html
<!-- RIGHT: mobile-first, consistent ordering -->
<h1 class="text-4xl md:text-5xl lg:text-7xl">

<!-- WRONG: random ordering, desktop-first -->
<h1 class="lg:text-7xl text-4xl md:text-5xl">
```

---

<a id="font-loading"></a>
## 8. Font Loading Strategy

If the fonts load slowly, the hero animation plays with fallback fonts and then "flashes" when the custom fonts arrive. This is called FOUT (Flash of Unstyled Text) and it ruins the first impression.

### Preconnect + font-display: swap

```html
<head>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@600;700&family=Plus+Jakarta+Sans:wght@400;500;600&display=swap" rel="stylesheet">
</head>
```

`display=swap` shows fallback text immediately and swaps in custom fonts when loaded. This prevents invisible text (FOIT) but can cause a visible font swap.

### Mitigating FOUT in the hero

To prevent the hero from "jumping" when fonts load:

**Option A — Delay hero animation**: Don't start the hero entrance animation until fonts are loaded:

```tsx
const [fontsLoaded, setFontsLoaded] = useState(false);

useEffect(() => {
  document.fonts.ready.then(() => setFontsLoaded(true));
}, []);

// Only trigger entrance animation when fontsLoaded is true
```

**Option B — Match fallback metrics**: Choose fallback fonts that closely match the custom font's metrics (x-height, width, weight). For Playfair Display, `Georgia` is a close match. For Plus Jakarta Sans, `system-ui` is reasonable.

### Only load the weights you use

Never load all weights. Specify only the weights used in the design:

```
Playfair Display: 600 (section titles), 700 (hero headline)
Plus Jakarta Sans: 400 (body), 500 (labels, buttons), 600 (emphasis, nav CTA)
```

Each additional weight adds ~20-40KB to the initial load.

---

<a id="section-transitions"></a>
## 9. Section Transitions

How sections connect visually matters. AI stacks sections with hard color-change edges that feel like separate pages glued together.

### Techniques for smooth transitions

**Background color alternation** (most common, simplest):
- Alternate between base and elevated colors
- The color change itself creates separation — no extra elements needed
- Add a subtle 1px border at the top of elevated sections if the contrast isn't visible enough

**The consistent-border technique**:
- Same background for adjacent sections, separated by a thin horizontal border (1px, border-subtle color)
- Creates a quieter, more editorial feel
- Best for sections with similar visual weight

**The gradient bleed** (use sparingly):
- Instead of a hard color switch, the bottom 80-120px of section A gradually blends into section B's background color
- Creates a "dissolve" effect
- Only use this for 1-2 transitions per page — if every transition bleeds, none stand out

**The full-width break**:
- A narrow full-width strip (the credibility bar, a pull quote, a single stat) breaks two content sections with a visually distinct band
- The strip uses a distinctly different background color or treatment
- Creates visual punctuation — like a paragraph break in writing

### Spacing between sections

There should be NO additional margin between sections — the sections' own internal padding creates the visual separation. If two sections feel "too close," increase the bottom padding of the first one. Adding margin between sections creates unpredictable gaps, especially on mobile.

---

<a id="photography"></a>
## 10. Using Real Photography

The default guidance says "no stock photos." But some users HAVE real photos — product photography, team photos, workspace shots. Here's how to integrate them without falling into stock-photo territory.

### When photos help

- **Product photography**: If the client sells a physical product and has professional shots, use them. A SaaS screenshot, a food photo, a jewelry close-up — real product imagery builds trust.
- **Space/environment**: A photo of the actual office, workshop, or store can establish credibility. The visitor sees "this is a real place."
- **Process documentation**: Photos of the actual process (someone evaluating jewelry, a chef in the kitchen, a consultation in progress) prove legitimacy.

### When to skip photos

- No professional-quality photos available (phone snapshots will hurt more than help)
- The photos are generic (team standing in front of a white wall, handshake poses)
- The photos require explanation (they don't communicate anything at a glance)

### How to integrate photos well

**Full-bleed treatment**: One large photo spanning the full width of a section. No border, no border-radius, no shadow. The photo IS the section background, with a subtle dark overlay and text on top.

```css
.photo-section {
  position: relative;
  background-image: url(...);
  background-size: cover;
  background-position: center;
}
.photo-section::before {
  content: '';
  position: absolute;
  inset: 0;
  background: rgba(0, 0, 0, 0.5); /* or a gradient overlay */
}
```

**Contained treatment**: Photo within the content area, alongside text (2-column layout). Photo should have border-radius matching the design system (8-12px). NO shadow (shadows on photos look dated).

**Detail crop**: Instead of showing the full photo, crop to an interesting detail. A close-up of gold texture, a hand holding a product, a detail of the workspace. Crops are more visually interesting than full shots.

### Photo anti-patterns

- ❌ Multiple small photos in a grid (looks like a gallery, not a landing page)
- ❌ Circular crop on photos (2015 design pattern, looks dated)
- ❌ Photo + border + shadow + border-radius (over-styled)
- ❌ Team photos where people are looking at the camera and smiling (feels staged)
- ❌ Photos with embedded text or watermarks
- ❌ Photos used purely as decoration (a photo should communicate something specific)
