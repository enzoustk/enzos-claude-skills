# Consolidation Rules — Merging References into One Design System

Use this reference during Steps 5-6 (assembly and validation).

## Single-Reference Mode

When only 1 reference was approved, skip consolidation entirely. Map tokens directly from the extraction. In the output header, set `**Mode:** Single-reference`. No conflict resolution needed.

## Multi-Reference Consolidation

When 2+ references provide conflicting values for the same token, resolve by category:

### Typography
If 2+ references share the same display font → use it. If all differ → choose the one matching the stated tone (luxury=serif, tech=geometric sans, playful=rounded). Body font: prioritize readability — choose the most legible option.

### Colors
Use the palette from the reference whose tone most closely matches the target brand. Secondary references contribute accent variations. If user expressed a preference ("I like Site A's dark theme"), that reference gets priority.

### Spacing
Use the base unit from the reference with the most consistent system. If all use 8px → use 8px. Mixed (4px and 8px) → default to 8px (safer).

### Motion
Match the majority's motion philosophy. If 2/3 references use subtle hovers → the system is subtle. One outlier with heavy animation doesn't override.

### Layout
Hero layout from the reference closest to the project's industry. Container widths from the reference with the most professional spacing.

### Structure
When references have different structural patterns for the same
section type (e.g., one hero is centered-typographic, another is
split with image):
- Use the pattern from the reference closest to the project's
  industry and content availability (if the user has no product
  photos, a split-image hero from the reference is irrelevant)
- If both patterns are viable, present the choice to the user:
  "Reference A uses centered hero, Reference B uses split layout.
  Which structure fits your content better?"
- Per-section structure can be cherry-picked from different
  references: hero structure from A, features grid from B,
  CTA composition from C
- Document all structural choices in references-report.md

### Documentation Rule
Every conflict and its resolution goes in `references-report.md` with reasoning. Cherry-picked elements ("typography from A, colors from B") must be documented.

---

## Edge Cases

### Conflicting aesthetics (Edge Case 5)

User approves dark minimal AND colorful maximalist references. Present the conflict: "Reference A is dark/minimal, B is colorful/maximal. Which direction fits your brand?" Use the chosen one as primary. Cherry-pick specific elements from secondary ("Typography from A, accent approach from B"). Document all decisions.

### Low-quality reference (Edge Case 6)

User provides a URL that fails quality criteria (template, dated, poor accessibility). Flag concerns honestly: "This appears to use a Wix template." Recommend adding a higher-quality primary reference. If user insists: extract anyway, but weight higher-quality references more heavily. Document: `"[URL] — quality concerns: [list]. Weighted lower."`.

### No results for industry (Edge Case 7)

Expand to adjacent industries in concentric circles:
- Pet grooming → pet services → veterinary → pet products → local services
- Agricultural equipment → industrial products → B2B equipment → manufacturing

If adjacent industries also fail: search for general pages matching the TONE ("best minimal dark landing page [year]"). Flag to user: "Limited gallery representation for your industry. References from [adjacent industry] match your tone but need more adaptation."

### Incomplete extraction (Edge Case 8)

Typography and colors extracted but spacing/motion/imagery missing. Fill gaps with front-end-craft defaults:
- Spacing: 8px grid (4, 8, 12, 16, 24, 32, 48, 64, 80, 96, 120, 160px)
- Motion: Cinematic level
- Imagery: typographic-only

Mark clearly in `design-system.md`:
- `## Typography [REFERENCE-DERIVED]`
- `## Spacing [DEFAULT — no reference data]`

Build skills check the tag and apply design system values where `[REFERENCE-DERIVED]`, own defaults where `[DEFAULT]`. Flag incompleteness to user.
