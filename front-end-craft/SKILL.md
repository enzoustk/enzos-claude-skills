---
name: frontend-craft
description: Create immersive, professional frontend interfaces with depth, 3D effects, scroll-driven animations, and cursor-reactive elements — not flat AI-generated aesthetics. Trigger when building any web interface, page, component, dashboard, or site needing high visual quality. Trigger phrases — "make it look professional", "design system", "visual identity", "immersive", "3D effects", "parallax", "scroll animation", "motion design", "website bonito", "UI/UX design", "make it pretty", "polish the interface", "visual upgrade", "componentes estilizados", or avoiding generic aesthetics. Also trigger for React/HTML where visual craft matters. Does NOT trigger for CSS bugfixes, layout debugging, responsive fixes, or adding a single dark-mode toggle — only for building or redesigning visual systems. Defines HOW things look and feel; pairs with domain-specific skills (like landing-page-craft) that define WHAT to build.
---

# Frontend Craft

Create frontend interfaces that are immersive, dimensional, and professionally designed. This skill defines the visual system — colors, typography, spacing, depth, motion, and interactivity — that makes any web interface feel alive and hand-crafted rather than AI-generated.

This skill answers the question: **"How should this look and feel?"** It pairs with domain-specific skills (like `landing-page-craft`) that answer **"What should this contain?"**

## When This Skill Is Loaded

Before writing any code, read references based on the immersion level:

### Always read (all levels):
1. **`references/anti-patterns.md`** — guardrails obrigatórios. Every item is a pattern AI defaults to that makes output look generic. Treat each as a hard constraint.
2. **`references/implementation-details.md`** — spacing system, light/dark themes, mobile design, button proportions, accessibility, Tailwind patterns, font loading.
3. **`references/custom-backgrounds.md`** — background surface treatments (tonal gradient, mesh gradient, dot grid, noise, accent edge glow) per immersion level.
4. **`references/decorative-elements.md`** — accent lines, glow effects, gradient borders, geometric shapes, gradient text accent.

### Read if Cinematic or Maximal:
5. **`references/parallax-and-3d.md`** — parallax (mouse + scroll), CSS 3D transforms, cursor customizado, magnetic buttons, 3D tilt cards. Skip Three.js sections unless Maximal.
6. **`references/scroll-animations.md`** — scroll-driven animation philosophy, useScrollProgress hook, text reveals, Lenis smooth scroll, GSAP/Framer Motion patterns.
7. **`references/transitions-and-atmosphere.md`** — clip-path transitions, sticky sections, grain/orbs/vignette atmosphere, performance rules, mobile degradation strategy.

### Read if Maximal only:
8. Three.js sections within `references/parallax-and-3d.md` (setup, scenes, rules).

## Step 1 — Define the Visual Identity

Before designing anything, extract or confirm these from the user's context. If information is missing, ask — don't invent.

| Input | What you need | If missing |
|-------|--------------|------------|
| **Color palette** | Background, accent, text colors (hex values) | Ask, or propose based on industry and tone |
| **Typography** | Display font + body font, or the desired tone | Propose a dual-font pairing based on tone |
| **Tone** | The visual personality (luxury, tech, playful, editorial, bold) | Infer from context |
| **Theme** | Dark or light base | Ask, or infer from brand |
| **Immersion level** | How interactive/3D the interface should be | Default to "cinematic" |

### Immersion levels

Every interface gets depth and interactivity — the intensity varies:

| Level | When to use | What it includes |
|-------|------------|-----------------|
| **Subtle** | Conservative brands, older audiences, data-heavy interfaces, dashboards | Smooth scroll (Lenis), scroll-driven opacity/translate, 3D tilt on hover (cards), ambient grain texture. No Three.js, no custom cursor. |
| **Cinematic** (default) | Most products, sites, marketing pages | Everything in Subtle + parallax layers (mouse + scroll), magnetic buttons, split text reveals, clip-path transitions, custom cursor (desktop), gradient orbs, vignette. |
| **Maximal** | Tech, gaming, crypto, agencies, portfolios, "wow factor" | Everything in Cinematic + Three.js backgrounds, horizontal scroll sections, WebGL particles, full scroll-driven timeline with scrub. |

## Step 2 — Build the Color System

Define these layers (adapt values to the user's palette):

```
BACKGROUNDS (depth hierarchy):
  Base:             [darkest/lightest — the canvas]
  Elevated:         [cards, sections — 2-3 tones shifted]
  Interactive:      [hover state]

ACCENT (brand's signature — max ~15% of visible area):
  Primary:          [main accent — CTAs, key highlights]
  Hover:            [lighter/brighter variant]
  Subtle:           [low-opacity for backgrounds/borders: rgba(accent, 0.08)]

TEXT (NEVER pure #FFFFFF or #000000):
  Primary:          [main readable text]
  Secondary:        [descriptions, supporting]
  Tertiary:         [labels, captions, metadata]

BORDERS:
  Subtle:           [barely visible structural]
  Active:           [accent-tinted for focus/hover]
```

**The 15% rule**: The accent color is a spotlight, not a floodlight. It appears on CTA buttons, key numbers, active states, and almost nowhere else. If the accent is everywhere, it guides nothing.

**Light vs Dark**: For dark themes, "Base" is near-black (#09090B, not #000000) and depth goes lighter. For light themes, "Base" is off-white (#FAFAFA, not #FFFFFF) and depth goes whiter. See `references/implementation-details.md` → "Light Theme Architecture".

**Contrast**: Every text/background pair must pass WCAG AA (4.5:1 body text, 3:1 large text). See `references/implementation-details.md` → "Accessibility Baseline".

### Background treatments

A `background-color` is a starting point, not a finished surface. Custom background treatments use subtle gradients, textures, or patterns to make each section feel intentional — not generated. The treatment intensity scales with the immersion level:

| Level | Background treatment |
|-------|---------------------|
| **Subtle** | Tonal gradients — 2-3 shades of the base color blended via radial/linear gradient (2-5% tonal difference) |
| **Cinematic** | Tonal gradients + mesh gradients (multi-point radial gradients with desaturated accent), dot grids, or accent edge glows |
| **Maximal** | Everything in Cinematic + WebGL-generated backgrounds, animated gradient meshes |

**The rule**: Treatments use the BASE palette at very low contrast (2-5% tonal difference). They should be invisible on a phone screenshot but felt on a desktop. If you can clearly SEE the pattern, it's too strong.

**Not every section needs treatment.** Sections with heavy reading (FAQ, long arguments) stay on clean base backgrounds. Sections of visual impact (hero, final CTA) earn the richest treatment. The contrast between treated and clean backgrounds is part of the visual rhythm.

See `references/custom-backgrounds.md` for five techniques with CSS code: tonal gradient, mesh gradient, dot grid, noise texture, accent edge glow.

## Step 3 — Build the Typography System

### Dual-font system

One display font (personality) + one body font (readability):

```
DISPLAY (headlines, hero text, standout numbers):
  Font:             [distinctive — NOT Inter, Roboto, Arial, Open Sans, Montserrat, Poppins]
  Usage:            Page/section titles, hero headline, featured numbers ONLY
  Never use for:    Buttons, labels, body text, nav items, paragraphs

BODY (everything else):
  Font:             [clean, readable sans-serif]
  Usage:            Paragraphs, buttons, nav, labels, descriptions, inputs
```

**Font pairing by tone:**

| Tone | Display | Body |
|------|---------|------|
| Luxury/Refined | Playfair Display, Cormorant, Libre Baskerville | Plus Jakarta Sans, DM Sans, Outfit |
| Modern/Tech | Space Grotesk, Sora, Clash Display | Inter (ok here), Geist, Satoshi |
| Bold/Impactful | Archivo Black, Cabinet Grotesk, Bebas Neue | Work Sans, Source Sans 3, Nunito Sans |
| Editorial/Magazine | Fraunces, Lora, Source Serif 4 | Fira Sans, Karla, Public Sans |
| Playful/Friendly | Fredoka, Baloo 2, Comfortaa | Quicksand, Lexend, Atkinson Hyperlegible |

### Typographic scale

Dramatic jumps between levels — not incremental 2px steps:

```
Hero/Page title:        48-72px desktop / 32-44px mobile   (display font)
Section title:          36-48px desktop / 28-36px mobile   (display font)
Subtitle:               18-22px                             (body font)
Body:                   16-18px                             (body font)
Label/Overline:         12-13px uppercase, letter-spacing 0.1em (body font)
Button text:            16-18px                             (body font)
```

## Step 4 — Build the Spacing System

All spacing values come from an **8px grid**: 4, 8, 12, 16, 24, 32, 48, 64, 80, 96, 120, 160px. Never use values like 15px, 30px, 50px.

**The proximity principle** — related elements cluster tight, unrelated ones separate:

```
Title → Subtitle:           12-16px   (tight — same visual group)
Subtitle → Content:         32-48px   (wide — new group)
Content → CTA:              32-48px   (wide — deliberate pause)
CTA → Microcopy:            8-12px    (tight — microcopy supports CTA)
```

**Section padding tiers** — NEVER the same on all sections:

```
Tier 1 (breathing):    120-160px   →  Hero, spacious sections
Tier 2 (standard):     80-96px    →  Content sections
Tier 3 (compact):      48-64px    →  Transition sections, footer
```

See `references/implementation-details.md` → "Spacing System" and "Text Spacing Relationships" for complete rules.

## Step 5 — Motion Design

### The three axes of immersion

From `references/scroll-animations.md`:
1. **Depth (Z-axis)**: Elements exist in layers. Parallax, perspective, 3D transforms.
2. **Scroll (Time-axis)**: Animation proportional to scroll position, not binary on/off.
3. **Reaction (Input-axis)**: Page responds to cursor, touch, device orientation.

### The golden rule

**Every effect rewards a visitor action.** Scrolled → something revealed. Moved mouse → something reacted. Hovered → element responded dimensionally. If an effect plays without visitor input, it should be subtle ambient atmosphere (grain, gradient orbs) — not a main visual event.

### Motion identity per element type

Don't animate everything the same way. Each element type gets a motion treatment that matches what it IS:

**Hover states (dimensional, not flat)**:
- Buttons: magnetic pull toward cursor + translateY(-2px) + glow
- Cards: 3D tilt following cursor + glare highlight + internal Z-depth layers
- Nav links: underline reveals with clip-path
- Icons: scale(1.15) + rotateY(15deg)

**Scroll reveals (progressive, not binary)**:
- Use scroll progress (GSAP ScrollTrigger `scrub` or Framer Motion `useScroll`), not IntersectionObserver
- Text: word-by-word reveal, gradient fill, or clip-path
- Cards: scale(0.92→1) + opacity, scrubbed to scroll
- Numbers: spring counter with slight overshoot

**Section transitions (cinematic, not hard cuts)**:
- Clip-path reveals (circle, diagonal, inset)
- Sticky sections where next slides over
- Scale-down of current + fade-in of next
- Never a hard color-change line between sections

### 3D technique decision — which technique for which element

Not everything deserves 3D. Use the wrong technique and the page feels like a tech demo. Use the right one and the visitor doesn't notice the technique — they notice the quality.

| Element | CSS 3D transforms | Three.js / WebGL | Skip 3D entirely |
|---------|-------------------|------------------|------------------|
| **Cards** | Always — tilt on hover + internal Z-depth layers | Never | — |
| **Hero headline** | Perspective entrance (`rotateX(5deg)→0`) + text-shadow depth | Never | If immersion level is Subtle |
| **Hero background** | Parallax layers + mesh gradient are enough for Cinematic | Particles, mesh surfaces, abstract geometry — Maximal only | Never skip entirely — at minimum use background treatments |
| **Section containers** | Scroll-driven `rotateX` for "page turn" feel on select sections | Never | Most sections — reserve for 1-2 high-impact transitions |
| **Icons** | `scale(1.15) + rotateY(15deg)` on hover | Never | If the section is text-heavy (FAQ, long arguments) |
| **Numbers / stats** | Spring counter with overshoot is the motion — no 3D transforms | Never | Let the value speak |
| **Buttons** | Magnetic pull + `translateY(-2px)` + glow | Never | — |

**Rule**: CSS 3D transforms are the workhorse (every immersion level). Three.js is the exception (Maximal only, hero only, one scene per page). If you're debating "CSS 3D or Three.js?" the answer is CSS 3D.

See `references/parallax-and-3d.md` → "3D com CSS e WebGL" for all implementation code.

### Transition assignment — which transition for which section pair

Section transitions are not interchangeable decorations. Each type communicates a different relationship between sections. Assign them based on what the sections ARE, not randomly.

| Section pair | Recommended transition | Why |
|-------------|----------------------|-----|
| **Hero → first content** | Scale-down + blur of hero, OR sticky hero with next section scrolling over | The hero is the "portal" — it recedes as the visitor enters the content |
| **Content → content (same depth)** | Clip-path reveal — diagonal or inset from bottom | Sections at the same depth "unveil" each other |
| **Content → elevated section** | Sticky current + elevated slides over | The elevated section "comes forward" — literally overlays |
| **Elevated → base section** | Scale-fade: elevated scales down slightly as base fades in | The elevated section "settles back" |
| **Any → Final CTA** | Clip-path circle reveal from center | The CTA is a focal point — the circle draws the eye inward |
| **Thin sections (credibility bar)** | No cinematic transition — simple scroll entry with subtle fade | Thin transitional sections don't warrant heavy transitions |

**Rule**: Max 2-3 cinematic transitions per page. The rest use simple scroll-entry reveals. If every transition is dramatic, none of them are.

See `references/transitions-and-atmosphere.md` → "Transições Cinematográficas entre Seções" for clip-path, sticky, scale, and horizontal scroll code.

**Atmosphere (ambient, passive — the exception to the "action→reward" rule)**:
- Grain texture: 2-3% opacity, fixed overlay
- Gradient orbs: blurred circles, accent color at 10-15% opacity, slow float (8-10s)
- Vignette: radial gradient darkening edges

### Technical defaults

```
Smooth scroll:      Lenis (duration 1.2, expo ease-out)
Scroll-driven:      GSAP ScrollTrigger (scrub: 1) OR Framer Motion (useScroll)
3D easing:          cubic-bezier(0.03, 0.98, 0.52, 0.99)
Reveal easing:      cubic-bezier(0.16, 1, 0.3, 1)
Mouse tracking:     throttled to ~30fps
Animated props:     ONLY transform and opacity — never layout properties
Performance floor:  60fps sustained (test with CPU throttle 4x)
```

See `references/parallax-and-3d.md` for parallax and 3D tilt code, `references/scroll-animations.md` for scroll progress hooks, Lenis config, and GSAP/Framer Motion patterns, and `references/transitions-and-atmosphere.md` for section transitions, atmosphere effects, performance optimization, and degradation strategy.

### Visual finishing elements — the decorative layer

Beyond motion and transitions, polished pages have a layer of decorative visual elements — accent lines, glow effects, gradient borders, geometric shapes. These are the details that make a page feel hand-crafted. They are NOT content and NOT atmosphere — they are structural decoration that reinforces visual hierarchy.

| Element | What it is | Where to use | Where NOT to use |
|---------|-----------|-------------|-----------------|
| **Accent line** | Thin line (1-2px, 40-80px) in accent color | Above overlines, beside section titles, as hero decoration | More than 2-3 per page |
| **Gradient border** | 1px border using pseudo-element gradient | Featured cards, primary argument, hero CTA area | Every card — defeats highlighting purpose |
| **Glow effect** | `box-shadow` with accent at 5-15% opacity | Behind cards on hover, behind CTA (subtle pulse), active nav | As a persistent visible halo — hover/focus only |
| **Decorative dots** | Small circles (4-8px) in accent/tertiary color | Corner of hero, beside step numbers, as timeline dots | Scattered randomly — each must feel intentional |
| **Geometric shapes** | Subtle SVG shapes (circles, rings, crosses) at 3-8% opacity | Hero background parallax layer, behind section titles | As foreground elements — always behind content |
| **Gradient text accent** | `background-clip: text` gradient on a keyword | One word per page max — hero headline accent word | Multiple words, body text, or subtitles |

**Rules**:
- Decorative elements use accent or tertiary colors ONLY, at low opacity (3-15%)
- Each element must reinforce hierarchy — accent lines point to content, glows highlight interactive elements
- Max 4-5 unique decorative element types per page. More becomes visual noise.
- All CSS-only (pseudo-elements, box-shadow, border-image, SVG). Zero JS, zero performance cost.

See `references/decorative-elements.md` for implementation code.

## Step 6 — Implementation

### Tech defaults

- **React** (functional components + hooks) or plain **HTML/CSS/JS**
- **Tailwind CSS** + CSS custom properties for design system
- **Lenis** for smooth scroll
- **GSAP + ScrollTrigger** OR **Framer Motion** for scroll-driven animations
- **Three.js** (Maximal level only — hero backgrounds, particle systems)
- **Lucide React** for icons
- **Google Fonts** for typefaces
- **Vite** as bundler

### Implementation order

1. **Foundation**: project setup, fonts, CSS custom properties, base background confirmed
2. **Smooth scroll**: Lenis — changes how everything feels
3. **Spacing system**: define the 8px grid scale before any section
4. **Components top-down**: build each component, checking visual rhythm
5. **Immersive layer**: parallax, 3D tilt, custom cursor, atmosphere
6. **Mobile design pass**: degradation strategy, not just responsive reflow. See `references/implementation-details.md` → "Mobile Design"
7. **Performance pass**: 60fps, LCP < 2.5s, composited properties only
8. **Accessibility pass**: focus-visible, semantic HTML, headings, prefers-reduced-motion

See `references/implementation-details.md` for Tailwind patterns, useInView hook, font loading strategy, button proportions, and light/dark theme architecture.

## Quality Checklist

**Typography & Color**
- [ ] Display font appears ONLY on titles and featured numbers
- [ ] No pure `#FFFFFF` or `#000000` anywhere
- [ ] Accent color covers ≤15% of visible area
- [ ] No banned fonts (Inter, Roboto, Arial as primary — see anti-patterns)
- [ ] Dramatic size contrast between heading levels

**Spacing**
- [ ] All values from 8px grid (4, 8, 12, 16, 24, 32, 48, 64, 80, 96, 120, 160)
- [ ] Title-to-subtitle gap ≈ HALF of subtitle-to-content gap
- [ ] No two adjacent sections have identical padding

**Immersion & Motion**
- [ ] Smooth scroll (Lenis) active on desktop
- [ ] No two sections use the exact same entrance animation
- [ ] Cards use 3D tilt on hover with glare (desktop)
- [ ] Buttons have magnetic hover (desktop)
- [ ] Hero and final CTA use background treatments beyond flat solid color
- [ ] Ambient atmosphere present (grain, orbs, or vignette)
- [ ] Section transitions are cinematic (not hard cuts)
- [ ] All scroll animations are proportional to scroll (not binary on/off)
- [ ] Every effect rewards a visitor action (except subtle ambient)
- [ ] 60fps sustained during scroll
- [ ] Only `transform` and `opacity` animated
- [ ] At least 2-3 decorative visual elements present (accent lines, glow effects, gradient borders)
- [ ] Decorative elements use accent/tertiary colors at low opacity (3-15%)
- [ ] `prefers-reduced-motion` disables ALL effects

**Mobile & Accessibility**
- [ ] Works on 375px without overflow
- [ ] Touch targets ≥44px
- [ ] Font ratios compress on mobile (headlines ~40% smaller, body barely changes)
- [ ] All text passes WCAG AA contrast
- [ ] :focus-visible on every interactive element
- [ ] Heading hierarchy sequential (h1 → h2 → h3)
- [ ] `<a>` for navigation, `<button>` for actions

**Buttons**
- [ ] Horizontal padding 2-2.5× vertical padding
- [ ] Max ONE enhancement (shadow OR glow OR border)
- [ ] Icons left of text, 20px, same color as text

## Output Reference — Minimum by Level

**Subtle** (dashboard, data-heavy):
Lenis smooth scroll + scroll-driven opacity reveals + 3D tilt on cards + grain texture (2-3%) + tonal gradient backgrounds. No custom cursor, no Three.js, no cinematic transitions.

**Cinematic** (default — most products):
Everything in Subtle + parallax layers (mouse + scroll) + magnetic buttons + split text reveals + clip-path transitions between 2-3 section pairs + custom cursor (desktop) + gradient orbs + vignette + accent decorative elements (lines, glow, gradient borders).

**Maximal** (agency, gaming, portfolio):
Everything in Cinematic + Three.js hero background (one scene) + horizontal scroll section + WebGL particles.
