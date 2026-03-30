# Research Sources — Galleries, Queries, and Selection Criteria

Use this reference during Step 2 (research) and Step 3 (selection).

## Curated Gallery Sources

| Priority | Source | URL | Strength | Best for |
|----------|--------|-----|----------|----------|
| 1 | Awwwards | awwwards.com | Highest quality, scored (use ≥8.0) | Any industry |
| 2 | Godly | godly.website | Curated modern/cutting-edge | Tech, SaaS, agency, minimal |
| 3 | SiteInspire | siteinspire.com | Large collection, filterable | Any industry — filter by style + subject |
| 4 | Lapa.ninja | lapa.ninja | Landing pages specifically | LP references — filter by color/industry |
| 5 | Landingfolio | landingfolio.com | LP-specific, by industry | Industry-specific LP patterns |
| 6 | SaaS Landing Page | saaslandingpage.com | SaaS-specific | SaaS, B2B, developer tools |
| 7 | Minimal Gallery | minimal.gallery | Clean, minimal designs | Luxury, editorial, refined |
| 8 | Httpster | httpster.net | Diverse styles | Eclectic, creative brands |
| 9 | One Page Love | onepagelove.com | Single-page sites | Single-page LPs and portfolios |

## Query Construction Patterns

### Tier 1 — Reliable queries for web search

| Pattern | Example |
|---------|---------|
| `"[industry] website" awwwards` | `"jewelry website" awwwards` |
| `"best [industry] website design [year]"` | `"best jewelry website design 2025"` |
| `"[industry] landing page design inspiration"` | `"jewelry landing page design inspiration"` |
| `"[industry] website" godly OR awwwards OR siteinspire` | Combines multiple galleries |
| `"[competitor name] website"` | `"tiffany website"` → get real URL |

### Tier 2 — Refining after initial results

| Pattern | Example |
|---------|---------|
| `"[site name] design case study"` | `"acme jewelry design case study"` |
| `"[industry] website design" behance case study` | Finds case studies with design details |
| `"[tone] website design" [year] award` | `"minimal luxury website design 2025 award"` |

### Tier 3 — Fallback for low-representation industries

| Pattern | Example |
|---------|---------|
| `"[adjacent industry] website design" awwwards` | `"luxury retail website design" awwwards` |
| `"best [tone] landing page [year]"` | `"best dark minimal landing page 2025"` |
| `"[industry] branding" behance` | Finds branding projects linking to live sites |

**Note:** `site:awwwards.com [industry]` may work but is unreliable — galleries use AJAX/internal filters. Use as supplement only.

After finding site mentions in articles/lists, use `web_fetch` on the actual URL for extraction.

## Acceptance Criteria

Accept a reference if ALL apply:
- **Professional design** — not a template (ThemeForest, Wix, Squarespace), not a student project
- **Current** — design feels modern, within ~2 years
- **Quality** — ≥8.0 on Awwwards (if sourced there); otherwise editorial judgment on typography, spacing, hierarchy
- **Relevant** — same industry OR same tone/aesthetic as target
- **Extractable** — enough content/sections for a meaningful design system (not just a splash page)

## Rejection Criteria

Reject if ANY apply:
- Template site (identifiable ThemeForest/Wix/Squarespace template)
- Obvious design issues (broken responsive, poor contrast, inconsistent spacing)
- Purely experimental/art (no commercial design intent)
- Older than ~3 years with dated patterns
- Near-duplicate of an already-selected reference

## Quantity and Diversity Rules

**Minimum:**
- Standard: 2 references (preferred: 3 — two from industry, one adjacent for diversity)
- Single-reference mode: 1 is acceptable if user explicitly confirms "this is exactly the direction I want." Skip consolidation in Step 5, flag in output: `**Mode:** Single-reference — less diversity in design decisions.`

**Maximum:** 6 references (beyond 6, diminishing returns).

**Diversity:** Max 2 with near-identical aesthetics. At least 1 must differ in color theme (dark/light), layout approach (minimal/rich), or typography style (serif/sans-serif).
