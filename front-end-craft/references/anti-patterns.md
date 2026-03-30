# Anti-Patterns — What Makes a Landing Page Look "AI-Generated"

## How to use this file

These are patterns that Claude produces by default when it has no
visual direction. They indicate that a design decision was NOT made
— the model is reproducing the statistical average of its training data.

**If a design system was extracted from real references:**
These anti-patterns do NOT apply automatically. If the approved
reference uses Inter, white backgrounds, or a 3-card grid with icons
— that is an intentional decision derived from a real reference, not
a model default. Respect the design system.

**If NO design system exists:**
These anti-patterns are active guardrails. Avoid all of them. They
exist to force intentional choices when there is no reference to
derive decisions from.

---

This file is the most important reference in this skill. Every item below is a pattern that AI-generated landing pages default to. They are the reason clients say "this looks like it was made by a robot." Treat each one as a hard constraint — if you catch yourself producing any of these, stop and redesign.

## The Diagnosis

AI-generated landing pages fail because they optimize for completeness instead of persuasion. They include every possible section, make everything symmetrical, use safe defaults, and produce pages that look like every other AI page. The result is technically correct and emotionally dead.

The core problem is **pattern convergence** — without strong constraints, generative models converge on the same handful of layouts, color schemes, font choices, and section structures. This file exists to break that convergence.

---

## Layout Anti-Patterns

### ❌ The Equal-Weight Stack
**What it looks like**: Every section has the same padding (usually 80-120px), the same background color, and the same visual weight. The page reads as a vertical list of identical blocks.

**Why it's wrong**: Real designers create visual rhythm — alternating dense sections with breathing room, dark backgrounds with light ones, full-width compositions with narrow containers. The eye needs contrast to stay engaged.

**The fix**: Vary padding between sections (80px → 140px → 100px). Alternate background colors. Make the hero 100vh but the credibility bar only ~120px. Let some sections breathe while others are compact.

### ❌ The 3-Column Icon Grid
**What it looks like**: Three (or four) equal-width cards in a row, each with a colored icon in a circle, a bold title, and 2-3 lines of description. Usually labeled "Our Features", "Why Choose Us", or "Our Values".

**Why it's wrong**: This is the single most recognizable AI layout pattern. It appears in 90%+ of generated landing pages. Users have developed "AI blindness" to it.

**The fix**: Use vertical timelines for processes. Use asymmetric grids (60/40 alternating) for arguments. Use a single narrow column for FAQ. Use horizontal rows with separators for stats. Never put 3 equal cards with icons side by side.

### ❌ Total Symmetry
**What it looks like**: Everything is centered. Every section mirrors left-to-right. Cards are all the same size. Columns are all equal width.

**Why it's wrong**: Symmetry is easy and boring. Real editorial design uses tension — unequal columns, offset elements, alternating alignment. Asymmetry creates visual interest and guides the eye.

**The fix**: Alternate text alignment (left-aligned hero → centered stats → left-aligned FAQ). Use unequal column splits (60/40, 65/35). Let process steps zig-zag instead of stacking vertically centered.

### ❌ The Cookie-Cutter Sections
**What it looks like**: Every section follows the same pattern: overline, big title, subtitle, content grid, CTA. Rinse and repeat six times.

**Why it's wrong**: When every section has identical structure, the page has no personality. Each section should feel distinct while belonging to the same visual family.

**The fix**: Each section gets a different layout: hero (centered typography), credibility (thin horizontal bar), process (vertical timeline), arguments (asymmetric bento grid), stats (numbers with separators), FAQ (narrow accordion), final CTA (centered with generous whitespace).

---

## Color Anti-Patterns

### ❌ The Purple-Blue Gradient
**What it looks like**: Background gradients from purple to blue, or purple to pink. Sometimes as hero background, sometimes on buttons, sometimes as blob shapes.

**Why it's wrong**: This is the default "creative" palette that AI generators produce when no brand colors are specified. It's become a visual fingerprint of AI-generated content.

**The fix**: Use the client's actual brand palette. If no palette exists, propose one based on the industry and tone — not purple-blue. A dark base with a warm accent will always outperform gradient blobs.

### ❌ Pure Black and Pure White
**What it looks like**: Background `#000000`, text `#FFFFFF`. High contrast, technically accessible, emotionally cold.

**Why it's wrong**: Pure black looks unfinished. Pure white on dark backgrounds creates eye strain. Real design uses near-blacks (#09090B, #0A0A0A, #111111) and off-whites (#F4F4F5, #FAFAFA, #E5E5E5).

**The fix**: Always use off-black and off-white. The user won't consciously notice, but the page will feel "warmer" and more intentional.

### ❌ The Flat Uniform Background
**What it looks like**: Every section uses `background-color: var(--base)` or `background-color: var(--elevated)` as the complete background definition. The entire page is flat solid-color blocks stacked vertically. No gradients, no texture, no tonal variation — just alternating slabs of two colors.

**Why it's wrong**: Flat solid backgrounds are the visual equivalent of a blank wall. They work for app UIs where content density carries the design, but on marketing pages and landing pages, they signal "template." Real design treats the background as a surface with subtle visual interest — tonal shifts, material texture, or color fields that reinforce the brand. A page with only flat `background-color` feels like the developer stopped before finishing.

**The fix**: The hero section should have at minimum a tonal gradient (2-3 shades of the base color blended via radial gradient). The final CTA benefits from an accent edge glow (accent color at 3-5% opacity fading from one edge). Content sections alternate between clean backgrounds and subtly textured ones (dot grid, noise texture) to reinforce visual rhythm. The treatment should be nearly invisible — if the visitor consciously notices the background pattern, it's too strong. See `references/custom-backgrounds.md` for techniques and code.

### ❌ Accent Overload
**What it looks like**: The brand's accent color is used on buttons, icons, borders, badges, backgrounds, dividers, and hover states. The page is 40% accent color.

**Why it's wrong**: When the accent is everywhere, it guides nothing. The accent color should function like a spotlight — it points the eye to the most important elements (CTA buttons, key numbers, active states). If everything is lit, nothing stands out.

**The fix**: Cap accent usage at ~15% of visible surface area. The accent goes on CTA buttons, key stat numbers, active navigation items, and maybe one word in the hero headline. Everything else is the neutral palette.

### ❌ Random Colored Icons
**What it looks like**: Each feature card has an icon in a different color — blue, green, orange, purple — usually inside a colored circle or rounded square.

**Why it's wrong**: Multi-colored icons create visual noise and look like a SaaS template. They fragment the visual hierarchy instead of supporting it.

**The fix**: All icons use the same treatment: monochrome (tertiary text color by default, accent color for active/highlighted states), consistent stroke-width, consistent size. No circles or squares around them unless they're interactive buttons.

---

## Typography Anti-Patterns

### ❌ The Default Font Stack
**What it looks like**: Inter, Roboto, Arial, Helvetica, Open Sans, system-ui as the primary font. Sometimes Montserrat or Poppins.

**Why it's wrong**: These are the fonts that every UI framework and AI tool defaults to. Using them guarantees the page looks generic. They're fine for app UIs, but landing pages need personality.

**The fix**: Choose distinctive fonts that match the brand tone. See the font pairing table in SKILL.md. The display font should have character; the body font should be readable but not generic.

### ❌ Flat Type Hierarchy
**What it looks like**: Hero headline is 36px, section titles are 32px, subtitles are 24px. The sizes are close together and the page reads as monotone.

**Why it's wrong**: Typography drives hierarchy. When sizes are too close, nothing stands out. The hero headline should be dramatically larger than section titles, which should be dramatically larger than body text.

**The fix**: Create extreme contrast. Hero at 56-72px, section titles at 36-44px, body at 16-18px. The jump between levels should be obvious at a glance.

### ❌ Display Font Everywhere
**What it looks like**: The fancy display font (script, serif, display sans) is used for headlines, buttons, labels, nav items, FAQ questions, and body text.

**Why it's wrong**: Display fonts are designed for large sizes and limited use. When used everywhere, they lose their impact and hurt readability. It also makes the page look like someone discovered a font and couldn't stop using it.

**The fix**: The display font is sacred — section titles and hero headline only. Everything else uses the body font. This creates a clear hierarchy: see display font → this is important.

---

## Content Anti-Patterns

### ❌ Stock Photography
**What it looks like**: Generic photos of people shaking hands, smiling at laptops, standing in front of whiteboards, or holding coffee cups. Sometimes abstract close-ups of "technology."

**Why it's wrong**: Visitors recognize stock photos instantly. They signal "we didn't invest in real imagery." Worse, they add visual weight without communicating anything specific about the brand.

**The fix**: If the client has no real photos, don't use photos at all. A purely typographic page with strong color and whitespace looks more professional than one stuffed with Unsplash images. Photography earns its place only when it shows the actual product, team, or space.

### ❌ Corporate Buzzword Copy
**What it looks like**: "We deliver innovative solutions with cutting-edge technology to empower your business and drive sustainable growth through synergistic partnerships."

**Why it's wrong**: Nobody reads it. Nobody believes it. It says nothing specific. It could describe any company in any industry.

**The fix**: Write copy that describes what the visitor GETS in concrete terms. "Pagamento em 24 horas" beats "Processo de pagamento ágil e eficiente." "Fale direto com quem avalia" beats "Atendimento personalizado de excelência."

### ❌ The "Saiba Mais" CTA
**What it looks like**: Generic button text: "Saiba Mais", "Learn More", "Get Started", "Contact Us", "Enviar", "Submit".

**Why it's wrong**: The CTA is the most important piece of copy on the page. Generic text means the visitor doesn't know what will happen when they click. Specificity reduces friction.

**The fix**: The button text should describe the outcome: "Avaliar Meu Ouro", "Agendar Demonstração Grátis", "Receber Orçamento pelo WhatsApp", "Criar Minha Conta". If it doesn't start a sentence that describes what the user gets, rewrite it.

### ❌ Lorem Ipsum / Placeholder Text
**What it looks like**: Any text that isn't real copy: "Lorem ipsum dolor sit amet", "Your text here", "Description goes here", "Feature title".

**Why it's wrong**: It's incomplete work. The page isn't ready until every word is real, reviewed copy that serves the persuasion arc.

**The fix**: Write the actual copy. If the client hasn't provided it, draft it based on the brand, industry, and conversion goal. Every text element should be final before delivery.

### ❌ The "Our Values" Section
**What it looks like**: A section with 3-4 abstract values like "Innovation", "Integrity", "Excellence", "Teamwork" — each with an icon and a vague paragraph.

**Why it's wrong**: Nobody trusts a company because their website says they value "integrity." Values are proven by actions and social proof, not declared.

**The fix**: Replace with concrete proof: real numbers (clients served, years of operation), specific process descriptions (how the product works), or direct customer outcomes. "500+ clientes atendidos" beats "Compromisso com a excelência" every time.

---

## Animation Anti-Patterns

### ❌ The Flat, Static Page
**What it looks like**: Sections appear with a simple fade-up. Hover states are color changes. The scroll is mechanical. Nothing responds to the cursor. There's no depth, no layers, no parallax. The page is a vertical document with CSS transitions sprinkled on top.

**Why it's wrong**: In 2025+, a flat page IS the generic page. Visitors have been trained by award-winning sites to expect depth, scroll-driven reveals, and reactive elements. A flat page — even with good typography and colors — signals "template" or "AI-generated." Flatness is the new purple gradient.

**The fix**: Every page needs at minimum: smooth scroll (Lenis), parallax depth in the hero, 3D tilt cards on hover, scroll-driven text reveals, cinematic section transitions, and ambient atmosphere (grain/orbs/vignette). See `references/parallax-and-3d.md`, `references/scroll-animations.md`, and `references/transitions-and-atmosphere.md` for implementation.

### ❌ The Uniform Fade-Up (AOS Syndrome)
**What it looks like**: Every single element enters with the same animation: `fade-in + translateY(20px)`. The entire page is a monotone wave of identical reveals, like items on a conveyor belt.

**Why it's wrong**: When everything animates the same way, the animation becomes invisible — the brain stops registering it after the second section. Variety in motion creates engagement. Each section should have a UNIQUE motion identity.

**The fix**: Assign each section a distinct technique: hero gets parallax layers, process steps get scroll-driven timeline that draws itself, cards get 3D tilt on hover with glare, numbers get spring counter with overshoot, final CTA gets clip-path circle reveal. No two adjacent sections should use the same animation technique.

### ❌ Hard Cuts Between Sections
**What it looks like**: Section A ends. Section B starts. There's a hard color-change line between them. Scrolling feels like flipping through slides.

**Why it's wrong**: Hard cuts break the immersive illusion. The page should feel like one continuous flowing experience — not a stack of separate blocks glued together.

**The fix**: Every pair of adjacent sections should have a transition technique: clip-path reveal (circle, diagonal, inset), sticky section where the next slides over, scale-down of current + fade-in of next, or at minimum a gradient color bleed. See `references/transitions-and-atmosphere.md` → "Transições Cinematográficas."

### ❌ Binary On/Off Animation (IntersectionObserver Only)
**What it looks like**: Element is invisible. Scroll reaches it. IntersectionObserver fires. Element fades in. Done. The animation has two states: hidden and shown.

**Why it's wrong**: Binary reveals are the flat version of scroll animation. Immersive pages use **scroll progress** — the animation is proportional to scroll position. The element transforms WHILE the visitor scrolls, not after they arrive.

**The fix**: Replace IntersectionObserver with scroll progress (GSAP ScrollTrigger `scrub`, Framer Motion `useScroll`, or CSS `animation-timeline: scroll()`). Text fills with color progressively. Elements scale from 0.9 to 1 proportionally. Clip-paths expand in sync with scroll. The visitor controls the pacing.

### ❌ Misused Spring/Bounce Easing
**What it looks like**: Exaggerated bounce on section entrances. Cards that overshoot and wiggle. Elastic easing on every transition.

**Why it's wrong**: Heavy spring on page-level transitions feels toy-like and uncontrolled. However, SUBTLE spring (2-3% overshoot) on micro-interactions adds organic life.

**The fix**: Reserve spring easing for specific moments: number counters (slight overshoot past target then settle), magnetic buttons (elastic return to center), 3D tilt (soft spring settle when mouse leaves). Never on section-level entrances or scroll reveals.

### ❌ Animation That Blocks Content
**What it looks like**: Visitor must wait for a loading screen, a logo animation, or a scroll-triggered sequence to finish before they can read content or click the CTA.

**Why it's wrong**: Every second of blocked interaction is lost visitors. The content and CTA must be accessible during and after animations. Animation enhances consumption — it never gates it.

**The fix**: Content renders immediately. Animations enhance the reveal but don't delay access. If a scroll-driven animation is in progress, the underlying content is still readable. The CTA is always clickable, even mid-animation.

### ❌ Lifeless Hover States
**What it looks like**: Hover effects are limited to `color: accent` on text or `background-color: slightly-different` on buttons. Every interactive element has the same hover treatment — a flat color swap.

**Why it's wrong**: Hover is one of the primary moments where the page REACTS to the visitor. Flat hover states make the page feel dead. In an immersive page, hover states are 3D, directional, and physical.

**The fix — dimensional hover**:
- **CTA buttons**: magnetic pull toward cursor + translateY(-2px) + glow intensifies
- **Cards**: 3D tilt following cursor position + glare highlight + internal elements at different Z-depths
- **Nav links**: underline reveals with clip-path or draws from center outward
- **FAQ questions**: text glows in accent, chevron rotates with spring easing
- **Social icons**: scale(1.15) + rotateY(15deg) — "turns toward you"
- **Process step numbers**: color intensifies + subtle scale(1.05)

### ❌ Decoration Without Reaction
**What it looks like**: Floating particles, rotating geometric shapes, pulsing dots — animation that plays on a loop regardless of what the visitor does. The effects would be identical whether someone is looking at the page or not.

**Why it's wrong**: The golden rule of immersive design: every effect should reward an action. Scrolled → something revealed. Moved mouse → something reacted. If the effect is purely passive, it's a screensaver, not interaction design.

**The fix**: Two categories of animation are valid:
1. **Reactive**: responds to scroll, cursor, click, or touch. This is the core of immersion.
2. **Ambient**: very subtle, very slow background atmosphere (grain texture, gentle gradient orb float, vignette). This is valid because it creates atmosphere WITHOUT competing for attention.

Any animation that is neither reactive nor subtle ambient should be cut.

---

## Section Structure Anti-Patterns

### ❌ The Standard LP Template
**What it looks like**: Hero → Features (3 cards) → "How It Works" (3 steps horizontal) → Testimonials (carousel) → Pricing (3 tiers) → CTA → Footer. This exact sequence, in this exact order, with these exact section names.

**Why it's wrong**: This is the template every AI has been trained on. It's the Bootstrap landing page, the Tailwind UI template, the Framer starter — all converged into one Platonic form of "a landing page." When a visitor sees this structure, they pattern-match it to every other generic page they've seen, and their trust drops.

**The fix**: Start from the persuasion arc (Attention → Trust → Clarity → Conviction → Evidence → Resolution → Action) and choose ONLY the sections that serve your specific conversion goal. A luxury service might need: Hero → Credibility → Process → FAQ → CTA (skip testimonials if you don't have real ones, skip pricing if it's custom). A SaaS product might need: Hero → Demo/Visual → Social Proof → Features (NOT as cards) → Pricing → CTA. The section list should emerge from the strategy, not from a template.

### ❌ The Overloaded Page
**What it looks like**: 8-10 sections trying to cover every possible angle: hero, about us, features, how it works, testimonials, team, pricing, blog preview, newsletter, partner logos, FAQ, contact form, map, and footer.

**Why it's wrong**: Every additional section dilutes the ones before it. Attention is finite. A landing page is a funnel — adding more sections widens the funnel mouth but doesn't improve the exit rate. Worse, more sections means more chances for the visitor to lose interest before reaching the CTA.

**The fix**: Cap at 6-7 sections including hero and footer. Before adding a section, ask: "If I remove this, will fewer people click the CTA?" If the answer is "probably not," cut it. Common sections that can be safely cut: "About Us" (nobody cares about your story before they trust your product), "Our Team" (this is a landing page, not LinkedIn), "Blog Preview" (distracts from conversion), "Partner Logos" (unless they're instantly recognizable and trust-building).

### ❌ Identical Internal Structure
**What it looks like**: Every section follows the same internal skeleton: small uppercase overline → big title → medium subtitle → content block → optional CTA. Six sections, six times the same scaffolding.

**Why it's wrong**: When the internal rhythm repeats mechanically, the page feels auto-generated — because it is. The visitor subconsciously detects the pattern and disengages. Real editorial design varies the entry point of each section.

**The fix**: Vary the internal structure:
- **Hero**: overline → headline → subheadline → CTA (the one place the full scaffolding is justified)
- **Credibility bar**: NO title. Just the content (icons + text) in a thin strip. It needs no introduction.
- **Process**: overline → title → then straight into the steps. No subtitle — the steps ARE the explanation.
- **Arguments**: title only (no overline, no subtitle) → then straight into the asymmetric grid. Let the cards speak.
- **Numbers**: NO title, NO overline. Just the numbers. They're self-explanatory. A section that says "Nossos Números" above three stats is adding a label to something obvious.
- **FAQ**: overline → title → accordion. No subtitle before the questions.
- **Final CTA**: title (as a question) → one line of microcopy → button. That's it. Maximum three elements.

The principle: the more a section's content is self-explanatory, the less scaffolding it needs. Numbers don't need a title. A credibility bar doesn't need an overline. A FAQ doesn't need a subtitle.

### ❌ Uniform Container Width
**What it looks like**: Every section's content sits in the same `max-width: 1200px` container, centered. The page is a single vertical column of identical width.

**Why it's wrong**: When every section has the same horizontal footprint, the page lacks spatial variety. The eye never encounters a change in "texture" — it's one continuous ribbon of same-width content.

**The fix**: Vary container widths by section purpose:
- **Hero**: wider margins (10vw+ on each side), or max-width ~800px for pure typographic compositions
- **Credibility bar**: full-width background, content in standard container
- **Process**: narrower container (~1000px) — process steps don't need to fill the page
- **Arguments**: standard width (~1100px) for grids
- **FAQ**: deliberately narrow (~680-720px) — long lines of text are hard to read, and the narrow container signals "intimate, focused content"
- **Final CTA**: narrow (~600-640px), centered — creates a "funnel" effect as the page converges to the action
- **Full-width breaks**: consider making one section (credibility bar, or a dramatic quote) span the full viewport width as a visual break

The narrowing of containers toward the end creates a subconscious funnel effect — the page physically converges toward the CTA.

---

## Component Anti-Patterns

### ❌ Carousels / Sliders
**Why they're wrong**: Users don't click to the second slide. Autoplay is aggressive. Manual navigation has near-zero engagement rates. They add complexity without value.

**The fix**: If you have multiple testimonials, stack them vertically or show the best 2-3 statically.

### ❌ Email Capture Popups
**Why they're wrong**: They interrupt the persuasion flow, annoy visitors, and signal desperation. The conversion mechanism should be embedded in the page flow, not forced.

**The fix**: CTA buttons in the natural flow of the page. If email capture is the goal, embed the form in the final CTA section — don't pop it up.

### ❌ Fake Countdown Timers
**Why they're wrong**: Visitors know they're fake. They erode trust instead of creating urgency.

**The fix**: If there's real urgency (limited offer, seasonal), state it in copy. If there isn't, don't manufacture it.

### ❌ Fake Testimonials with Stock Photos
**Why they're wrong**: Generic headshots with generic quotes ("Great service! Highly recommend! ★★★★★") are transparent fabrication.

**The fix**: Use real testimonials with full names (ask the client). Or skip testimonials entirely and use quantitative social proof (number of clients, ratings from verified platforms). No testimonials is better than fake testimonials.

---

## The Meta Anti-Pattern

The single biggest mistake is **playing it safe**. AI defaults to the middle of every spectrum: medium colors, medium contrast, medium spacing, medium animation, medium everything. The result is a page that's inoffensive and forgettable.

Good design takes positions. Dark background OR light, not medium gray. Dramatic headline OR compact density, not medium text. Generous whitespace OR controlled density, not "comfortable" padding on everything.

And in 2025+: immersive depth OR deliberate brutalist flatness — not the accidental flatness of "I didn't know how to add depth." A flat page is only acceptable as a conscious artistic choice (brutalist design). If the page is flat because the developer didn't implement parallax, tilt, scroll-driven animation, and section transitions, that's not minimalism — it's incompleteness.

Every design decision should be an active choice, not a default.
