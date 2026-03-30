# Section Blueprints — Landing Page Architecture

Each section below is a template to be **adapted** to the user's brand, content, and conversion goal. The layouts, copy examples, and specifications are starting points — not rigid prescriptions. What IS rigid: the anti-patterns they avoid and the visual rhythm they create.

## Table of Contents
1. [Navbar](#navbar)
2. [Hero](#hero)
3. [Credibility Bar](#credibility-bar)
4. [Process / How It Works](#process)
5. [Arguments / Why Us](#arguments)
6. [Social Proof / Numbers](#social-proof)
7. [FAQ](#faq)
8. [Final CTA](#final-cta)
9. [Footer](#footer)
10. [Floating CTA](#floating-cta)

---

<a id="navbar"></a>
## 1. Navbar

**Purpose**: Navigation + persistent brand presence + always-available CTA.

### Layout
- Fixed top, z-index 1000
- Height: 64-80px
- Container: max-width matches page container (1100-1200px), centered
- Flex: justify-between, align-center

### Behavior
- **Top of page**: transparent background, no border
- **After scroll (~50px)**: solid background (base color), subtle bottom border, optional backdrop-blur
- Transition: background 300ms ease

### Content
- **Left**: Brand name or logo. Use the display font for the brand name (keep it short — just the name, not a tagline).
- **Center or Right**: 3-4 anchor links that scroll to sections. Body font, secondary text color. Hover: primary text color, 150ms transition. Keep labels short: "Como Funciona", "Por Que Nós", "Dúvidas" — not "Nossos Diferenciais e Valores Corporativos".
- **Right (after links)**: Compact CTA button (the main conversion action). Smaller padding than the hero CTA.

### Mobile (< 768px)
- Logo left, hamburger icon right
- Menu opens as fullscreen overlay (base background), links stacked vertically centered, CTA button at bottom
- Open animation: fade-in 200ms
- Close on link click (smooth scrolls to section)

---

<a id="hero"></a>
## 2. Hero

**Purpose**: Stop the scroll. In 3 seconds, communicate what this is and why it matters.

### Layout
- Height: 100vh (full viewport)
- NO background image, photo, or illustration (unless the client has real product photography)
- Content centered vertically and horizontally
- Generous lateral margins (min 10vw desktop, 24px mobile)
- Pure typographic composition + whitespace

### Content stack (top to bottom)

1. **Overline** (optional):
   - Brand name or category in uppercase
   - Style: body font, 12-13px, weight 600, uppercase, letter-spacing 0.1-0.15em, tertiary text color
   - Optional: small decorative element (a thin 40px line in accent color above or beside)

2. **Headline**:
   - Max 2 lines. Concrete, benefit-oriented.
   - Style: display font, 56-72px desktop / 36-44px mobile, weight 700, primary text color
   - letter-spacing: -0.02 to -0.03em (tighten for display)
   - line-height: 1.05-1.15 (tight for impact)
   - ONE word or short phrase can be accent-colored (max one per headline)
   - **Good**: "Seu ouro tem valor.\nA Áurea tem a melhor oferta."
   - **Bad**: "A Melhor Plataforma Inovadora de Soluções em Ouro do Brasil"

3. **Subheadline**:
   - Max 2 sentences. Expands on the headline with specifics.
   - Style: body font, 18-20px, weight 400, secondary text color, max-width 540-580px, line-height 1.5-1.7
   - **Good**: "Avaliação profissional, pagamento imediato e total transparência."
   - **Bad**: "Oferecemos os melhores serviços do mercado com excelência e compromisso."

4. **CTA button**:
   - Accent background, dark text (or inverted if light theme)
   - Padding: 16px 36px, border-radius 8px, body font weight 600, 16px
   - Hover: slightly lighter/brighter accent, 150ms transition
   - Below or beside: microcopy in tertiary color, 13px ("Resposta em 5 min", "Grátis por 14 dias", "Sem compromisso")

5. **Scroll indicator** (bottom of viewport):
   - Subtle chevron or arrow pointing down
   - Animation: translate-y oscillation (2s, ease-in-out, infinite)
   - Color: tertiary text, 20-24px

### Entrance animation
- Staggered fade-in + translateY for each element:
  - Overline: delay 0ms, duration 600ms
  - Headline: delay 150ms, duration 700ms
  - Subheadline: delay 300ms, duration 600ms
  - CTA: delay 450ms, duration 500ms
- Easing: cubic-bezier(0.16, 1, 0.3, 1)
- translateY: start at 12-20px, end at 0

### Immersive hero enhancements

On top of the base hero, add these depth and interaction layers:

- **Parallax depth layers**: 3 layers responding to mouse position (background 8-15px, content 2-5px, accent -2-3px). Code: `frontend-craft` → `references/parallax-and-3d.md` → "Parallax com mouse".
- **3D perspective container**: `perspective: 1200px`, entrance `rotateX(5deg)→0` — "falling into place" feel.
- **Custom cursor activation**: dot + expanding ring, ring expands on CTA hover. Code: `frontend-craft` → `references/parallax-and-3d.md` → "Custom cursor".
- **Magnetic CTA**: button moves slightly toward cursor. Code: `frontend-craft` → `references/parallax-and-3d.md` → "Magnetic buttons".
- **Custom background surface**: mesh gradient + tonal gradient — never flat `background-color`. Code: `frontend-craft` → `references/custom-backgrounds.md`.
- **Ambient atmosphere**: 2-3 gradient orbs (accent 10-15% opacity, 8-10s float) + grain (2-3%) + optional vignette. Code: `frontend-craft` → `references/transitions-and-atmosphere.md` → "Camadas Atmosféricas".
- **Three.js background (Maximal only)**: WebGL scene reacting to mouse. Code: `frontend-craft` → `references/parallax-and-3d.md` → "Three.js".

### Hero exit transition

As the visitor scrolls past the hero, it should not simply disappear. Options:
- **Scale down + blur**: `scale(0.95)` + `filter: blur(4px)` + opacity fade, driven by scroll progress
- **Parallax push**: hero content scrolls UP at 0.5x speed while the next section overlays it
- **Sticky + overlay**: hero stays sticky while the credibility bar scrolls OVER it

---

<a id="credibility-bar"></a>
## 3. Credibility Bar

**Purpose**: Quick trust signals. Transitional section between hero and content.

### Layout
- Short height: 100-140px
- Background: elevated (one step lighter/darker than base)
- Top and bottom borders: subtle structural borders
- Content: flex row, centered, generous gap (48-80px between items)

### Content
- 3-4 trust indicators, each: icon (20px, accent color, thin stroke) + short text phrase
- Text: body font, 14px, weight 500, primary text color
- Examples by industry:
  - Service: "Empresa Registrada" / "Pagamento em 24h" / "Avaliação Gratuita"
  - SaaS: "10k+ Usuários" / "99.9% Uptime" / "Suporte 24/7"
  - E-commerce: "Frete Grátis" / "Troca em 30 dias" / "Compra Segura"

### What NOT to do
- Don't use big numbers here (save those for the Social Proof section)
- Don't use colored icon backgrounds
- Don't make this section tall or visually heavy — it's a whisper, not a shout

### Mobile
- 2 items per row, or stack vertically with horizontal separators

### Visual layer
- **Transition in**: Simple fade-in staggered (150ms between items). No cinematic transition — too thin.
- **3D / Decorative**: None. Trust signals should feel grounded and stable.
- **Background**: Elevated color, clean. No treatments.

---

<a id="process"></a>
## 4. Process / How It Works

**Purpose**: Show the visitor that the process is simple and non-threatening. Eliminate "it seems complicated" objections.

### Layout
- Base background color
- Generous vertical padding (120px desktop, 80px mobile)
- Container: max-width 1000px

### Content

1. **Overline**: "COMO FUNCIONA" (or equivalent)
2. **Section title**: Display font, accent or primary color
3. **3-4 steps** in a vertical layout with connecting elements

### Step layout options (choose one, never a horizontal 3-card grid):

**Option A — Zig-zag**
```
Step 01 ──────── aligned left
       │ (connector line)
       ──────── Step 02 aligned right
       │
Step 03 ──────── aligned left
```
- Numbers: display font, 48-64px, accent color
- Title: body font, 20px, weight 600, primary text
- Description: body font, 15-16px, weight 400, secondary text, max-width 480px
- Connector: thin vertical/diagonal line, border/divider color, ~48px height

**Option B — Vertical timeline**
```
●─── Step 01
│    Title + description
│
●─── Step 02
│    Title + description
│
●─── Step 03
     Title + description
```
- Dot: 8-12px circle, accent color
- Line: 1-2px, divider color
- Content offset from the timeline by 32-48px

**Option C — Numbered stack (offset)**
```
┌─ 01 ─────────────────────┐
│  Title + description      │
└───────────────────────────┘
        ┌─ 02 ──────────────────────┐
        │  Title + description       │
        └────────────────────────────┘
┌─ 03 ─────────────────────┐
│  Title + description      │
└───────────────────────────┘
```
- Cards with subtle background (elevated color), subtle border, 12px radius
- Numbers prominent and accent-colored

### Animation
- Each step fades in on scroll entry (IntersectionObserver)
- Stagger: 150ms between steps
- Connector lines can animate (draw-in effect) for extra polish

### Visual layer
- **Transition in**: Clip-path diagonal reveal, or staggered fade-in if already 2+ cinematic transitions.
- **3D**: Step numbers `rotateY(15deg)→0` on scroll. Connector lines draw-in. Steps stay flat.
- **Decorative**: Accent timeline dots (8-12px), optional accent line beside title.
- **Background**: Dot grid or clean base. See `frontend-craft` → `references/custom-backgrounds.md`.

---

<a id="arguments"></a>
## 5. Arguments / Why Us

**Purpose**: Give concrete reasons to choose this company/product over alternatives.

### Layout
- Elevated background (one step from base)
- Generous vertical padding (120px desktop)
- Container: max-width 1100px

### Content

1. **Overline**: "POR QUE NÓS" / "DIFERENCIAIS" / "WHY [BRAND]"
2. **Section title**: Display font, 36-44px

### Grid layout (NOT equal cards):

**Option A — Asymmetric bento**
```
┌──────── 60% ────────┐  ┌──── 40% ────┐
│  Argument 1 (large)  │  │  Argument 2  │
└──────────────────────┘  └──────────────┘
┌──── 40% ────┐  ┌──────── 60% ────────┐
│  Argument 3  │  │  Argument 4 (large)  │
└──────────────┘  └──────────────────────┘
```

**Option B — Feature/value with offset**
```
  [icon]  Title
          Description paragraph (max 3 lines)
          
  [icon]  Title
          Description paragraph
```
Left-aligned, stacked vertically, offset from left margin. Simple and editorial.

**Option C — Two-column alternating**
```
Text left          |  Visual/metric right
                   |
Visual/metric left |  Text right
```
Each row alternates which side has the primary content.

### Card styling (when using cards)
- Background: surface/elevated color
- Border: 1px subtle border
- Border-radius: 12px (max 16px)
- Padding: 32-40px
- Icon: 20-24px, accent color, thin stroke, top of card
- Title: body font, 18-20px, weight 600, primary text
- Description: body font, 15-16px, weight 400, secondary text

### Immersive card behavior

Cards in an immersive LP are not flat rectangles — they exist in 3D space:

- **3D tilt on hover**: ±6-8 degrees toward cursor + glare highlight. Code: `frontend-craft` → `references/parallax-and-3d.md` → "Cards e Elementos com Profundidade".
- **Internal depth layers**: icon `translateZ(40px)`, title `translateZ(25px)`, description `translateZ(10px)` — requires `transform-style: preserve-3d`.
- **Scroll entrance**: `scale(0.92)→1` + opacity, scrubbed to scroll (not binary), 100-150ms stagger.
- **Ambient glow**: on hover, radial gradient in accent color at 5-8% opacity behind the card.

### Copy rules
- Lead with what the CUSTOMER gets, not what the company has
- Max 3 lines of description per argument
- Avoid abstract words: "quality", "innovation", "excellence"
- Use concrete outcomes: "Pagamento em 24h", "Avaliação sem custo", "Fale com um humano"

---

<a id="social-proof"></a>
## 6. Social Proof / Numbers

**Purpose**: Build trust through scale, experience, and validation.

### Layout
- Base background
- Moderate vertical padding (80-120px)
- Container: max-width 900px, centered
- This section should be compact and impactful

### Content
- 3 (or 4 max) key metrics side by side (flex row)
- Each metric:
  ```
  [NUMBER]          ← Display font, 48-64px, weight 700, accent color
  [label]           ← Body font, 14px, weight 400, tertiary text
  ```
- Separators between metrics: thin vertical line, divider color, height 48px
- Examples: "R$ 2M+ / em ouro negociado", "500+ / clientes atendidos", "4.9★ / avaliação Google"

### Count-up animation
- Numbers animate from 0 to target when they enter the viewport
- Duration: 800ms, ease-out
- Use requestAnimationFrame, not setInterval
- Trigger once (disconnect observer after)

### If the client has testimonials
- Show 2-3 real testimonials below the numbers
- Each: quote text (body font, 16-18px, secondary text, italic optional) + name + role/context (13px, tertiary)
- Never use stock photos for testimonial avatars — initials in a circle or nothing
- Layout: stacked vertically or 2-column (not carousel)

### Mobile
- Stack metrics vertically with horizontal separators between them

### Visual layer
- **Transition in**: Clip-path inset reveal from bottom. Count-up triggers after reveal.
- **3D**: None — the spring counter IS the motion.
- **Decorative**: Vertical separators are sufficient. Optional accent edge glow from top.
- **Background**: Clean base or accent edge glow. See `frontend-craft` → `references/custom-backgrounds.md`.

---

<a id="faq"></a>
## 7. FAQ

**Purpose**: Kill remaining objections. Answer what the visitor is thinking but won't ask.

### Layout
- Elevated background
- Generous vertical padding (120px desktop)
- **Narrow container**: max-width 680-720px (comfortable reading width)

### Content

1. **Overline**: "DÚVIDAS FREQUENTES" / "FAQ"
2. **Section title**: Display font, 36px

3. **Accordion**:
   - 5-6 questions max (more feels overwhelming)
   - Question: body font, 16-17px, weight 500, primary text
   - Answer: body font, 15px, weight 400, secondary text, padding-top 12px, line-height 1.7
   - Separator between items: 1px divider color
   - Toggle icon: ChevronDown, 18px, tertiary color, rotates 180° on open
   - Open animation: height transition 250ms ease + content fade-in
   - Hover on question: text color shifts to accent, 150ms transition
   - Only one item open at a time (closing previous when opening new)

### Writing good FAQ copy
- Questions should be in the visitor's voice, not the company's: "Preciso ir presencialmente?" not "Nosso modelo de atendimento"
- Answers should be 2-4 sentences max, specific and concrete
- End the last FAQ answer with a soft nudge: "Ainda tem dúvidas? Fale conosco pelo WhatsApp."

### Visual layer
- **Transition in**: Scale-fade from previous section (scale down 5% + blur → FAQ fades in).
- **3D / Decorative**: None. Reading section — visual calm is mandatory.
- **Background**: Clean elevated base. No treatments.

---

<a id="final-cta"></a>
## 8. Final CTA

**Purpose**: Last push. The visitor has consumed all the content — now close.

### Layout
- Base background with accent edge glow (accent 3-5% opacity at top, fading to base by 30%) + tonal gradient. See `frontend-craft` → `references/custom-backgrounds.md` → "Accent Edge Glow".
- Generous vertical padding (120-160px)
- Container: max-width 600-640px, centered, text-align center

### Content
1. **Title**: Display font, 40-48px, primary text. Question format works well: "Pronto para [outcome]?"
2. **Subtitle**: Body font, 18px, secondary text. Reiterate the low-friction promise: "Avaliação gratuita, sem compromisso."
3. **CTA button**: Same style as hero CTA, slightly larger padding. Include channel icon if applicable (WhatsApp, email, phone).
4. **Microcopy below**: Business hours, response time, or "Sem compromisso" — tertiary text, 13px.

### Optional: CTA glow
- Subtle animated box-shadow on the button: pulsing accent color at low opacity
- Animation: 2s ease-in-out infinite, between `box-shadow: 0 0 0 0 rgba(accent, 0.2)` and `0 0 0 12px rgba(accent, 0)`
- Keep it subtle — it should attract attention, not distract

---

<a id="footer"></a>
## 9. Footer

**Purpose**: Legal closure and secondary navigation. Minimal.

### Layout
- Darkest background (one step darker than base, or same as sidebar/navbar)
- Top border: subtle structural border
- Padding: 40-56px vertical
- Container: max-width matches page

### Content
- **Row 1** (flex, space-between):
  - Left: brand name (body font, 16px, weight 600, primary text) + legal info (CNPJ, address — 13px, tertiary text)
  - Right: social icons (Instagram, WhatsApp, LinkedIn — 20px, tertiary color, hover: primary)
- **Separator**: 1px divider, margin 24px 0
- **Copyright**: body font, 12px, tertiary text, centered

### What NOT to include
- Massive multi-column footer with sitemap (this is a single-page LP)
- Newsletter signup (the conversion channel is already defined)
- Decorative elements or accent-colored backgrounds

### Visual layer
- **Transition**: None — structural, not cinematic. Social icons: `scale(1.15)` hover.
- **Decorative**: Top border only. No accent lines, no glows.

---

<a id="floating-cta"></a>
## 10. Floating CTA

**Purpose**: Always-available conversion action, visible during scroll.

### When to use
- When the conversion channel is WhatsApp, phone, or a booking link
- Not needed if the conversion is a form embedded in the page

### Specs
- Position: fixed, bottom 24px, right 24px
- Size: 56px × 56px circle
- Background: channel-appropriate color (WhatsApp: #25D366, phone: accent, booking: accent)
- Icon: 28px, white
- Shadow: 0 4px 12px rgba(0,0,0,0.25)
- Border-radius: 50%
- z-index: 9999
- Hover: scale(1.08), shadow expands, 200ms transition

### Behavior
- Hidden initially
- Appears after: first scroll event, or 2 seconds after page load
- Entry animation: fade-in + scale(0.8 → 1), 300ms
- Optional tooltip on hover: "Fale conosco" left-aligned tooltip

### Mobile
- Same position but smaller margins (bottom 16px, right 16px)
- Size can shrink to 48px × 48px if needed
- Must not overlap with content or nav elements
