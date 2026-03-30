---
name: landing-page-craft
description: Build high-conversion landing pages with professional structure, persuasion architecture, and real copywriting. Use this skill whenever the user asks to create a landing page, LP, sales page, capture page, product page, or any single-page site meant to convert visitors into leads or customers. Also trigger when the user mentions "página de vendas", "página de captura", "one-pager site", or wants to build a homepage with a clear conversion goal (WhatsApp, email signup, booking, purchase). Even if they just say "build me a site for my business" and it's clearly a single-page conversion flow, use this skill. This skill defines the WHAT — sections, persuasion arc, copy, conversion strategy. It relies on the frontend-craft skill for the HOW — visual system, motion, depth, interactivity.
---

# Landing Page Craft

Build landing pages that convert. This skill defines the structure, persuasion architecture, copywriting, and conversion strategy of a landing page — the WHAT. For visual execution — colors, typography, depth, motion, 3D, interactivity — it delegates to the `frontend-craft` skill.

**This skill is always used together with `frontend-craft`.** Load both. This skill decides what sections to build, what copy to write, and how to structure the persuasion arc. `frontend-craft` decides how it looks, moves, and feels.

## When This Skill Is Loaded

1. **Load the `frontend-craft` skill first** — it defines the visual system (colors, typography, spacing, motion, immersion). Follow its Steps 1-5 to establish the visual identity before building sections.

**If `frontend-craft` is not available**, apply these minimum visual defaults before proceeding:
- Fonts: Space Grotesk (display) + Inter (body)
- Theme: dark (#09090B base, #FAFAFA text)
- Immersion: Cinematic
- Spacing: 8px grid (see Step 4 of this skill for section padding tiers)
- Smooth scroll: Lenis (duration 1.2)
- Accent: infer from brand or use #3B82F6
2. **Read `references/section-blueprints.md`** — understand the section templates, layout options, and immersive enhancements for each section type.
3. Then follow this skill's steps below for LP-specific decisions: brand extraction, section selection, persuasion arc, copywriting, and conversion strategy.

## Step 1 — Extract LP-Specific Identity

On top of the visual identity extracted by `frontend-craft`, a landing page needs these additional inputs:

| Input | What you need | If missing |
|-------|--------------|------------|
| **Conversion goal** | The ONE action the visitor should take (WhatsApp, signup, book, buy) | Ask — this is non-negotiable |
| **Value proposition** | What the company does, for whom, and why they're different | Ask |
| **Target audience** | Who visits this page, what they need, what they fear | Ask or infer from industry |
| **Tone of voice** | How the brand speaks (formal, casual, bold, sophisticated) | Infer from brand name and industry |

### Optional but valuable

- Real numbers for social proof (clients served, revenue, ratings) — if not available, skip social proof section
- Real testimonials with full names — if not available, skip testimonials (fake ones destroy trust)
- FAQ content — if not available, draft based on industry-standard objections
- Competitor URLs the user likes or dislikes

### The conversion channel defines everything

The conversion goal shapes the entire page. Define it first:

| Channel | CTA text examples | Floating CTA | Form? |
|---------|------------------|--------------|-------|
| **WhatsApp** | "Falar pelo WhatsApp", "Avaliar Meu Ouro" | WhatsApp bubble (#25D366) | No — link to wa.me/ |
| **Email signup** | "Começar Grátis", "Criar Minha Conta" | No | Yes — embedded in final CTA section |
| **Booking/Calendar** | "Agendar Demonstração", "Marcar Consulta" | Phone or calendar icon | Optional — link to Calendly/etc |
| **Purchase** | "Comprar Agora", "Adicionar ao Carrinho" | No | No — link to checkout |
| **Phone call** | "Ligar Agora", "Falar com Consultor" | Phone icon | No — tel: link |

The CTA must appear at minimum **3 times**: hero, mid-page, and final CTA section.

## Step 2 — Select Sections (Less Is More)

A landing page is NOT a website with all possible sections. It's a **persuasion funnel** — every section exists to move the visitor one step closer to the CTA. If a section doesn't serve this, cut it.

### The persuasion arc

```
ATTENTION    →  Hero: stop scrolling, this is relevant to you
TRUST        →  Credibility bar: we're legitimate
CLARITY      →  How it works: it's simple, not scary
CONVICTION   →  Why us: concrete reasons to choose us
EVIDENCE     →  Social proof: others trust us (numbers, testimonials)
RESOLUTION   →  FAQ: kill remaining doubts
ACTION       →  Final CTA: do it now
```

### Section selection rules

**Hard cap: 6-7 sections including hero and footer.** Before adding a section, ask: "If I remove this, will fewer people click the CTA?" If probably not — cut it.

**Sections to SKIP when you don't have real content:**
- No real testimonials → skip testimonials (fake ones destroy trust)
- No meaningful numbers → skip social proof (inventing "1000+ happy clients" is transparent)
- Process is self-evident → skip "How It Works" (not every product needs it)
- Pricing is custom/negotiated → skip pricing table

**Sections to ALWAYS skip on a landing page:**
- "About Us" — nobody cares about your story before they trust your product
- "Our Team" — this is a landing page, not LinkedIn
- "Our Values" — "Integrity, Innovation, Excellence" convinces nobody
- "Blog Preview" — distracts from conversion
- "Partner Logos" — unless instantly recognizable

**A 4-section page (Hero → Why Us → FAQ → CTA) that's impeccable beats an 8-section page full of filler.**

### Section combination examples by industry

| Industry | Recommended sections |
|----------|---------------------|
| Service (joalheria, clínica, consultoria) | Hero → Credibility → Process → Why Us → FAQ → CTA |
| SaaS / App | Hero → Social Proof → Features (NOT cards) → Process → Pricing → CTA |
| E-commerce premium | Hero → Product showcase → Why Us → Testimonials → FAQ → CTA |
| Agency / Portfolio | Hero → Work showcase → Process → Numbers → CTA |
| Local business | Hero → Credibility → Why Us → FAQ → CTA |

### Example output: jewelry store (WhatsApp conversion)

Sections: Hero → Credibility Bar → Process → Why Us → FAQ → Final CTA
- Hero: "Seu ouro tem valor. A Áurea tem a melhor oferta." + CTA WhatsApp
- Credibility: "Empresa Registrada" / "Pagamento em 24h" / "Avaliação Gratuita"
- Process: 3-step timeline (Envie foto → Avaliação → Pagamento em 24h)
- Why Us: bento grid, 4 argumentos concretos
- FAQ: 5 perguntas na voz do visitante
- Final CTA: "Pronto para vender seu ouro?" + botão WhatsApp
- Floating: WhatsApp bubble (#25D366), aparece após primeiro scroll

## Step 3 — Write the Copy

All text must be real, finalized copy — never placeholder. The copy is the most important conversion lever, more important than design.

### Headline rules

- **Max 2 lines.** Short, punchy, concrete.
- Lead with what the VISITOR gets, not what the company does.
- One word or phrase can be accent-colored (max one per headline).
- **Good**: "Seu ouro tem valor.\nA Áurea tem a melhor oferta."
- **Bad**: "A Melhor Plataforma Inovadora de Soluções em Ouro do Brasil"

### CTA rules

- Be specific about what happens: "Falar pelo WhatsApp", "Agendar Demo Grátis"
- **NEVER** use: "Saiba Mais", "Clique Aqui", "Enviar", "Submit", "Learn More", "Get Started"
- Add microcopy below the CTA: "Sem compromisso", "Resposta em 5 min", "Grátis por 14 dias"

### Body text rules

- Max 2-3 sentences per block. Visitors scan, they don't read.
- Describe outcomes, not features: "Pagamento em 24h" not "Sistema de pagamentos rápido"
- **Kill corporate buzzwords**: "sinergia", "inovação", "excelência", "holístico", "cutting-edge", "state-of-the-art", "soluções", "empoderar"

### Copy approved vs. rejected

| ❌ Rejected (generic) | ✅ Approved (concrete) |
|----------------------|----------------------|
| "Somos especialistas em X" | "Avaliamos cada peça com precisão. Você recebe o valor real." |
| "Atendimento de qualidade" | "Fale direto com quem entende. Sem robô, sem espera." |
| "Os melhores preços do mercado" | "Cotação atualizada em tempo real. Sem surpresas." |
| "Entre em contato conosco" | "Receba sua avaliação agora pelo WhatsApp" |
| "Saiba mais" | "Quero vender meu ouro" |
| "Soluções inovadoras" | "Seu relatório pronto em 24h, direto no email" |

### FAQ copy rules

- Questions in the visitor's voice: "Preciso ir presencialmente?" not "Nosso modelo de atendimento"
- Answers: 2-4 sentences max, concrete
- 5-6 questions maximum — more feels overwhelming
- End the last answer with a nudge: "Ainda tem dúvidas? Fale conosco pelo WhatsApp."

## Step 4 — Define Section Architecture

Read `references/section-blueprints.md` for detailed layouts. Key principles:

### Visual rhythm — sections are NOT equal

Vary three dimensions between sections to create rhythm:

1. **Background**: alternate between base and elevated colors — go beyond flat `background-color`. Hero earns the most complexity, final CTA benefits from accent edge glow, content sections stay quieter. For techniques and code, see `frontend-craft` → `references/custom-backgrounds.md`.
2. **Padding**: tier 1 (120-160px) → tier 3 (48-64px) → tier 2 (80-96px) — never uniform
3. **Container width**: hero (narrow, ~800px) → credibility (full-width) → process (~1000px) → FAQ (~720px) → final CTA (~640px). The narrowing creates a subconscious funnel effect.

### Layout variety — no two sections share a pattern

| Section | Layout | NOT this |
|---------|--------|----------|
| Hero | Centered typographic composition | ❌ Split with image |
| Credibility | Thin horizontal bar, no title | ❌ Cards with icons |
| Process | Vertical timeline or zig-zag | ❌ 3-card horizontal grid |
| Why Us | Asymmetric bento grid (60/40) | ❌ 4 equal cards |
| Numbers | Horizontal with separators, no title | ❌ Cards with big numbers |
| FAQ | Narrow-column accordion | ❌ Grid of dropdowns |
| Final CTA | Centered, max whitespace | ❌ Full-width colored bar |

### Internal structure variety

NOT every section follows: overline → title → subtitle → content → CTA. Vary it:

- **Credibility bar**: NO title. Content only. Self-explanatory.
- **Numbers**: NO title, NO overline. The numbers speak.
- **Process**: Overline + title, then straight into steps. No subtitle.
- **FAQ**: Overline + title, then accordion. No subtitle.
- **Final CTA**: Title (as question) + one line microcopy + button. Three elements max.

### Immersive layer per section — 3D, transitions, and visual polish

Each section gets a specific immersive treatment. Don't apply effects uniformly — assign them based on the section's role in the persuasion arc. For technique implementation, see `frontend-craft` → `references/parallax-and-3d.md`, `references/scroll-animations.md`, `references/transitions-and-atmosphere.md`, and `references/decorative-elements.md`.

| Section | 3D / Motion | Transition IN | Decorative elements |
|---------|------------|---------------|-------------------|
| **Hero** | Parallax depth layers (mouse-reactive) + perspective entrance (`rotateX(5deg)→0`) + magnetic CTA. Maximal: Three.js background | — (first section) | Gradient orbs, accent line above overline, geometric shapes in parallax layer |
| **Credibility bar** | None — static is appropriate for trust signals | Simple fade staggered across items (no cinematic transition) | Subtle top/bottom borders only |
| **Process** | Connector line draw-in on scroll + subtle `rotateY` on step numbers | Clip-path diagonal reveal OR staggered fade-in | Accent-colored timeline dots, accent line beside title |
| **Arguments / Why Us** | Cards: 3D tilt on hover + internal Z-depth layers + glow on hover | Sticky previous section + this slides over | Gradient border on featured card, decorative dots at grid intersections |
| **Numbers / Stats** | Spring counter with overshoot — no 3D transforms on numbers | Clip-path inset reveal from bottom | Vertical separators are sufficient — no extra decoration |
| **FAQ** | Accordion open/close only — no 3D, no scroll effects | Scale-fade from previous section | None — clean surface for reading |
| **Final CTA** | CTA glow pulse + magnetic hover | Clip-path circle reveal from center | Accent edge glow background, gradient border on CTA area |

**Rules**:
- **Max 2-3 cinematic section transitions per page.** The rest use simple scroll reveals.
- **Reading-heavy sections (FAQ, long arguments) get zero visual noise.** Clean background, no decorative elements.
- **Hero and final CTA earn the richest treatment.** Everything in between is more restrained.
- **Cards always get 3D tilt on hover** (unless immersion level is Subtle).

## Step 5 — Build

### Implementation order (LP-specific)

Follow `frontend-craft` Steps 1-6 for visual foundation, then:

1. **Navbar**: fixed, transparent→opaque on scroll, logo, anchor links to sections, compact CTA, mobile hamburger. Mobile: CTA stays OUTSIDE hamburger menu, always visible.
2. **Hero**: 100vh, typographic (no stock photos), staggered entrance, CTA + microcopy, scroll indicator. Apply immersive enhancements from `references/section-blueprints.md` → "Immersive hero enhancements".
3. **Sections top-down**: build each selected section following `references/section-blueprints.md`. Check visual rhythm between sections. Apply cinematic transitions between every section pair.
4. **Floating CTA**: if WhatsApp/phone — fixed bottom-right, channel color, appears after first scroll. See blueprints → "Floating CTA".
5. **Footer**: minimal — brand name, legal info, social icons, copyright. NOT a sitemap.

### Anchor links and smooth scroll

```tsx
// Navbar links scroll to section IDs
<a href="#como-funciona" onClick={(e) => {
  e.preventDefault();
  document.getElementById('como-funciona')?.scrollIntoView({ behavior: 'smooth' });
}}>
  Como Funciona
</a>

// Each section has an ID
<section id="como-funciona">...</section>
```

Mobile hamburger menu: closes on link click, then smooth-scrolls to section.

## Step 6 — Quality Review (LP-Specific)

The `frontend-craft` skill handles visual/motion/spacing/accessibility checks. This checklist covers LP structure and conversion:

**Persuasion Arc**
- [ ] Page has ≤7 sections (including hero and footer)
- [ ] Section sequence follows a persuasion arc, not a template
- [ ] Every section serves one step of: Attention → Trust → Clarity → Conviction → Evidence → Resolution → Action
- [ ] No "filler" sections (About Us, Our Team, Our Values, Blog Preview)

**Section Structure**
- [ ] No adjacent sections share the same layout pattern
- [ ] No section uses a 3-column grid of cards with icons
- [ ] Container widths vary between sections
- [ ] Internal structure varies (not every section has overline+title+subtitle+content)
- [ ] At least one section has NO title (credibility bar or numbers)
- [ ] At least the hero and final CTA use a background treatment beyond flat solid color
- [ ] Each section has an intentional immersive treatment (3D effects, transition, decorative elements)
- [ ] Max 2-3 cinematic section transitions — the rest use simple scroll reveals
- [ ] Reading-heavy sections (FAQ) have zero decorative visual noise
- [ ] Visual rhythm alternates between dense and spacious sections

**Conversion**
- [ ] CTA appears at least 3 times (hero, mid-page, end)
- [ ] CTA text is specific (not "Saiba Mais" or "Learn More")
- [ ] CTA describes what HAPPENS when clicked
- [ ] Microcopy below CTA reduces friction
- [ ] Floating CTA (if applicable) is visible and functional
- [ ] All conversion links use correct format (wa.me/, tel:, mailto:, or destination URL)

**Copy**
- [ ] All text is real copy (zero lorem ipsum or placeholder)
- [ ] Headlines are max 2 lines, benefit-oriented
- [ ] No corporate buzzwords
- [ ] FAQ has 5-6 questions max, in visitor's voice
- [ ] No stock images or fake testimonials

**Navigation**
- [ ] Navbar has anchor links that smooth-scroll to sections
- [ ] Navbar transitions transparent→opaque on scroll
- [ ] Mobile hamburger menu works and closes on link click
- [ ] Footer is minimal (not a sitemap)

## Important Reminders

- **This skill + `frontend-craft` = complete LP.** This skill defines WHAT to build. `frontend-craft` defines HOW it looks. Both are needed.
- **The conversion goal drives everything.** Every section, every word, every animation exists to move the visitor toward the CTA. If it doesn't serve this, cut it.
- **Less is more.** 4 impeccable sections beats 8 mediocre ones. Cut before you add.
- **Copy is the #1 conversion lever.** A perfectly designed page with generic copy won't convert. A perfectly written page with decent design will.
- **No fake content.** No stock photos, no fake testimonials, no invented numbers, no lorem ipsum. If you don't have real content for a section, skip the section entirely.
- **The FAQ is a conversion tool, not an information dump.** Each question addresses an objection. The last answer nudges toward the CTA.
