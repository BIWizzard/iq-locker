# iq-locker — Captures

Quick captures and notes. Use `/om-cap` to append.

### 2026-04-12 13:21

before we move on to the rest of the flags, I want to take this opportunity to leverage Open Mind and Claude Flow and capture the fundamental change and clean up we did related to where the source of truth lies for the IQ system, no matter what project you're in. Where are the rules, the locker, and the updated guidelines? Where are the right destinations in the KGiQ project to look for answers related to branding and where not?

The color map from Figma we brought in, all of it. I want to capture what we learned and what we did, and I want it to be much easier to communicate to any agent in any project at any time where to use the design system and where to find the rules.

**Synthesized reference (for future agents):**

**Where the iQ Design System source of truth lives (as of 2026-04-12):**

- **Implementation reference (tokens, CSS, components, motion, dark mode, setup):** `KGiQ-LLC/iq-design-locker/docs/iq-brand-system.md` — the single doc that answers "how do I brand an iQ app?"
- **Brand identity reference (logo, dot-as-signature, tech-nerd aesthetic, voice, product family):** `KGiQ-LLC/branding/guidelines.md` (v3.0) — the doc that answers "who is KGiQ and how does it look?"
- **Canonical token CSS files:** `KGiQ-LLC/iq-design-locker/core/` (iq-tokens.css, iq-typography.css, iq-motion-tokens.css)
- **Product accent files:** `KGiQ-LLC/iq-design-locker/accents/` (kgiq, publish, booking, circle)
- **Logos (SVG sources):** `KGiQ-LLC/iq-design-locker/logos/` (kgiq/, products/, icons/)
- **Skills (agent routing):** `KGiQ-LLC/iq-design-locker/skills/` (7 active + 1 dormant)
- **Curation index:** `KGiQ-LLC/iq-design-locker/MANIFEST.md`
- **Vendoring into consumer projects:** `KGiQ-LLC/iq-design-locker/scripts/vendor-into.sh`

**Full product accent color map (from Figma, canonical):**
- KGiQ parent: Gold #FFD166 (active)
- iQ Booking: Teal #0D9488 (active)
- iQ Publish: Indigo #6366F1 (active)
- iQ Circle: Rose #EC4899 (active)
- Menu: Red #EF4444 (reserved)
- Family Finance: Green #22C55E (reserved)
- Ledger: Purple #A855F7 (reserved)
- Next?: Lime #84CC16 (reserved)
- Blue Variant (Tight Corridor): Blue #0284C7 (reserved)
- iQ Foundation default: #0066CC / #4DA3FF

**Where NOT to look (archived/legacy):**
- `KGiQ-LLC/branding/archive/` — stale pre-iQ docs (design-tokens.css, components-guide, etc.)
- `KGiQ-LLC/branding/logos/v1-archive/` — pre-rebrand logo assets
- `KGiQ-LLC/iq-design-locker/docs/history/` — superseded spec, brief, and components guide
- Any `--brand-*`, `--surface-*`, `--bg-*`, `--text-*` CSS tokens — these are the old namespace. Only `--iq-*` tokens are canonical.

**What changed this session (2026-04-12):**
- Created `iq-brand-system.md` (508 lines) — synthesized from cross-app audits of Booking + Publish
- Created `accents/iq-accent-circle.css` — rose #EC4899 from Figma product accent map
- Vendored locker into iQ Circle project
- Rewrote `guidelines.md` from v2.0 (624 lines, stale) to v3.0 (386 lines, brand identity only)
- Moved 3 legacy docs to `docs/history/` in the locker
- Archived 8 stale docs + v2.0 guidelines in `branding/archive/`
- Reorganized all logos into `logos/kgiq/`, `logos/products/`, `logos/icons/` in the locker
- Updated all 7 SKILL.md files to route to `iq-brand-system.md` instead of legacy docs
- Updated MANIFEST, README, vendor-into.sh for iQ Circle
- Documented cross-app drift (Booking vs. Publish) in `docs/plans/2026-04-12-cross-app-drift-report.md`

---
