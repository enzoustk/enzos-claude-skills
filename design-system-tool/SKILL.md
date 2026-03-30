---
name: design-system-tool
description: Research real reference websites and extract design systems (typography, colors, spacing, motion, layout) to drive unique visual output. Trigger when the user wants references before building, asks to "research design", "find inspiration", "extract a design system", "analyze reference sites", "look at real websites for inspiration", wants to avoid generic/AI-looking output, mentions "design system", "design tokens", "referências visuais", or wants the visual direction derived from real-world examples instead of defaults. This skill runs BEFORE front-end-craft and landing-page-craft — it produces the design system they consume. Does NOT trigger for building interfaces directly — that's front-end-craft. Does NOT trigger for LP structure/copy — that's landing-page-craft.
---

# Design System Tool

Research real reference websites, extract design tokens, and produce a documented design system that the build skills (`front-end-craft`, `landing-page-craft`) consume. This skill answers: **"What should the visual identity be based on?"**

## Position in the Ecosystem

```
design-system-tool  → researches + extracts → design-system/design-system.md
       ↓
front-end-craft     → consumes design system → defines HOW it looks
       ↓
landing-page-craft  → consumes design system → defines WHAT to build
```

| Skill | If absent |
|-------|-----------|
| **design-system-tool** | Build skills use internal defaults. Functional but "AI signature" aesthetics. |
| **front-end-craft** | landing-page-craft uses minimum defaults (Space Grotesk + Inter, dark, Cinematic). |
| **landing-page-craft** | front-end-craft works standalone for non-LP interfaces. |

### Output location

All outputs go to `design-system/` in the project root:

```
design-system/
├── design-system.md      ← primary source of truth (build skills check this path)
├── design-system.css     ← CSS custom properties for implementation
└── references-report.md  ← extraction notes and conflict resolutions
```

## When This Skill Is Loaded

Load references progressively — not all upfront:

1. `references/research-sources.md` — read before Step 2
2. `references/extraction-guide.md` — read before Step 4
3. `references/consolidation-rules.md` — read before Step 5
4. Templates in `templates/` — copy and fill during Steps 5-6

---

## Step 1 — Collect Project Context

Gather inputs to construct targeted research queries.

| Input | Required? | If missing |
|-------|-----------|------------|
| Brand name | Yes | Ask |
| Industry / vertical | Yes | Ask |
| Target audience | Recommended | Infer from industry |
| Tone (luxury, tech, playful, editorial, bold, minimal) | Recommended | Infer from brand + industry |
| Competitor / inspiration URLs | Optional | Skip |
| Existing brand assets (colors, fonts) | Optional | Skip |

**Output:** Structured brief — brand, industry, audience, tone, constraints, competitor URLs, preferences.

**Advance when:** Brand + industry confirmed. At least tone OR audience defined.

**Fallback:** If user gives minimal info, infer and present explicitly: "I'm inferring tone as luxury/trust based on jewelry industry — correct?" Proceed only after confirmation.

---

## Step 2 — Research References

Find 5-8 candidate reference sites from curated design galleries and web search.

**Read `references/research-sources.md` before starting this step.** It contains gallery URLs, query patterns, and acceptance criteria.

| Input | Output |
|-------|--------|
| Structured brief from Step 1 | 5-8 candidates with: URL, source, relevance note, visual category (dark/light, minimal/rich), design description |

**Advance when:** ≥5 candidates from ≥2 sources. Max 2 with near-identical aesthetics.

**Fallback:** If <5 results for exact industry → expand to adjacent industries → general tone searches. Flag cross-industry references to user. See `references/research-sources.md` for query tiers.

---

## Step 3 — Present References (GATE)

User selects 1-4 references to derive the design system from.

### How to present each reference

1. **Provide the clickable URL**
2. **Describe the design in specific terms** — not "beautiful modern site" but "Dark background (#0A0A0A), large serif typography (Playfair Display), monochromatic with gold accent, typographic hero, smooth scroll with subtle reveals"
3. **Instruct the user to open the links:** "Open each in your browser — text descriptions don't replace the visual experience."
4. **Highlight what's worth extracting:** "From this site, the most valuable for your project: [color system / typography / motion design]"

Recommend top 3 with justification. Flag issues ("experimental layout may not transfer to a conversion LP").

**Advance when:** ≥1 reference approved by user. See `references/research-sources.md` for quantity rules including single-reference mode.

### If user rejects ALL references (Edge Case 1)

1. Ask what they disliked — capture structured feedback (color? layout? industry fit?)
2. Re-run Step 2 with adjusted queries. Max 2 retry cycles.
3. If still no approval: user provides own URLs, OR proceed without design system (flag: "Output will use default aesthetic choices — the same fonts and effects I always use.")

---

## Step 4 — Extract Design Tokens

Fetch each approved site's HTML/CSS and extract tokens. Infer motion/imagery from code patterns.

**Read `references/extraction-guide.md` before starting this step.** It contains the reliability table, extraction methods, and difficult-scenario handlers.

| Input | Output |
|-------|--------|
| Approved reference URLs | Per-site extraction: typography, colors, spacing, motion, layout, imagery — each token tagged with method |

**Advance when:** Typography + colors extracted from each site. Missing categories documented.

**Fallback:** If site blocks extraction → Wayback Machine → gallery page data → web search for design system docs. See `references/extraction-guide.md` for full fallback chain.

### Extraction Honesty Rule

Tag every token with its extraction method:

| Tag | Meaning | Confidence |
|-----|---------|------------|
| `[PARSED]` | Extracted directly from CSS/HTML code | High — build skills trust this |
| `[INFERRED]` | Derived from code patterns, library presence, or CSS rules | Medium — build skills may prefer own defaults |
| `[GALLERY]` | From gallery listing, screenshots, or third-party descriptions | Low — build skills likely use own defaults |

---

## Step 5 — Assemble Design System

Synthesize per-site extractions into one coherent design system.

**Read `references/consolidation-rules.md` before starting this step.** It contains conflict resolution rules and single-reference mode.

**Copy `templates/design-system-doc.md` and `templates/design-system-code.css`** — fill with extracted tokens.

| Input | Output |
|-------|--------|
| Per-site extractions from Step 4 | `design-system/design-system.md` + `design-system/design-system.css` |

**Advance when:** Design system covers all 7 categories: typography, colors, spacing, imagery, interactions, layout, anti-pattern exceptions. Incomplete categories marked `[DEFAULT — no reference data]`.

**Fallback:** Fill gaps with front-end-craft defaults. Mark as `[DEFAULT]`. Build skills handle partial systems gracefully.

---

## Step 6 — Validate and Handoff

Validate, produce references report, write all files to `design-system/`.

| Input | Output |
|-------|--------|
| Design system from Step 5 + extraction notes | Final `design-system/design-system.md`, `design-system.css`, `references-report.md` |

Run the Quality Checklist below. Auto-fix WCAG contrast failures (darken/lighten text to meet 4.5:1, document adjustment). Present final design system to user for approval.

**Advance when:** Checklist passes. User approves.

**Fallback:** Contrast issues → auto-adjust and document. User rejects → discuss objections, adjust tokens, re-validate.

---

## Quality Checklist

### Design system completeness
- [ ] All 7 sections present (typography, colors, spacing, imagery, interactions, layout, anti-pattern exceptions)
- [ ] Each section tagged `[REFERENCE-DERIVED]` or `[DEFAULT — no reference data]`
- [ ] Every reference-derived token has a Method tag (`[PARSED]`, `[INFERRED]`, `[GALLERY]`)
- [ ] Every reference-derived token traces back to a specific reference URL

### Typography
- [ ] Display + body fonts identified with specific weights
- [ ] Fonts available on Google Fonts (or substitutes documented)
- [ ] Scale covers: hero, h2, body, small, overline (desktop + mobile)

### Colors
- [ ] Full palette with roles (base, elevated, accent, text hierarchy, borders)
- [ ] All text/background pairs pass WCAG AA (4.5:1 body, 3:1 large)
- [ ] Contrast failures auto-corrected and documented

### References
- [ ] ≥1 reference analyzed (2+ preferred, 3+ ideal)
- [ ] User approved selection (Step 3 gate passed)
- [ ] No more than 2 references with near-identical aesthetics

### Consolidation
- [ ] Every conflict documented with resolution reasoning
- [ ] Anti-pattern exceptions cite specific reference URLs
- [ ] Cherry-picked elements documented

### Output files
- [ ] `design-system.md` follows template exactly (with Method column)
- [ ] `design-system.css` has valid CSS custom properties
- [ ] `references-report.md` covers all references with extraction notes
- [ ] All files in `design-system/` directory

### Integration
- [ ] Build skills can consume output without manual interpretation
- [ ] Incomplete sections marked for build skill default fallback
- [ ] Files at `design-system/design-system.md` for detection
