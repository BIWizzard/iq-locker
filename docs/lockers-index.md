# iq-locker — Locker Instance Registry

Authoritative registry of locker instances built under the iq-locker framework. Each entry points to where the locker physically lives and what it covers. This is the framework's inventory — not to be confused with a project-level `CLAUDE.md` "Lockers" section, which is the master index that teaches agents which lockers are available *in a given consumer project*.

## Convention

- **Lockers live in consumer project repos, not in iq-locker itself.** The framework is reusable; the instances are tied to the content they curate.
- **iq-locker is the memory.** All ideation, planning, design audits, session logs, and decisions about every locker instance stay in this project's `docs/` tree regardless of where the locker itself physically lives. This keeps iterative thinking out of consumer project histories and in one coherent place.
- **Every locker entry includes a pointer to its `LINEAGE.md`** once built — the backwards-looking provenance audit that establishes canonical source of truth for each file in the locker.
- **A locker entry here is the framework's claim that the locker exists and is managed.** If a locker isn't in this index, the framework doesn't know about it.

## Status values

- `scoping` — first-instance decision made, design audit in progress
- `building` — construction underway (Phases A–D)
- `validated` — built and passed spawn-discoverability validation
- `live` — in active use by at least one consumer project
- `deprecated` — no longer maintained; marked for removal or archive

## Instances

### IQ Design System Locker

| Field | Value |
|-------|-------|
| **Status** | `validated` — Built 2026-04-11 (Waves 1-4, 18 tasks), consolidated 2026-04-12 (single-source-of-truth detour), Wave 5+6 wired + empirical spawn discoverability test **passed first try** 2026-04-12. Will flip to `live` after first Phase E consumer migration. |
| **Home** | `/Users/kmgdev/KGiQ-LLC/iq-design-locker/` |
| **Scope** | IQ brand, design tokens (colors, surfaces, spacing, radius, shadows), typography (three-font stack: Plus Jakarta Sans / Inter / JetBrains Mono), canonical component set, motion system, voice and tone, dot-as-signature product accent system, tech-nerd aesthetic, KGiQ parent brand layer (logos, palette, tagline) |
| **Canonical source status** | **This locker IS the canonical source.** Before its creation, no single canonical source existed — three drifted copies of `iq-tokens.css` were scattered across booking-module, iq-publish, and portfolio-front-end, each with different product accents baked in. The locker consolidates these, fixes the drift, and becomes the authoritative home. |
| **Known consumer projects** | `/Users/kmgdev/dev_projects/portfolio-front-end` (kgiq.dev), `/Users/kmgdev/dev_projects/iq-publish`, `/Users/kmgdev/dev_projects/booking-module` — currently using stray copies; will be reconciled to vendor from the locker in Phase E. `/Users/kmgdev/dev_projects/iq-circle` — new iQ-family product (reimagined from KGIQ CRM), vendored from locker at inception (2026-04-12). |
| **Authoring source (history)** | `/Users/kmgdev/dev_projects/booking-module/docs/superpowers/specs/2026-03-24-iq-design-system-spec.md` — where the iQ design system was first formalized during the iQ Booking rebrand |
| **Design audit** | `docs/plans/iq-design-system-brand-audit.md` (in this project) — five-project filesystem survey + Neon memory query + expert panel review findings + build plan |
| **Build plan** | Phases A (extract + canonicalize) → B (backwards-looking audit + `LINEAGE.md`) → C (wrap in SKILL.md) → D (discover + wire) → E (reconcile consumer projects). Details in the brand audit doc. |
| **Session history** | Will accumulate under `docs/sessions/` in this project — every iq-design-locker session (design, build, maintenance, reconciliation) is logged here, not in KGiQ-LLC. |
| **LINEAGE.md** | `KGiQ-LLC/iq-design-locker/LINEAGE.md` — 8 sections: (1) per-file provenance, (2) drift reconciliation, (3) consumer-project delta report, (4) completeness check, (5) spec verification, (6) quality-gate history, (7) 2026-04-12 reorganization + brand system consolidation (incl. §7.9 path resolution table), (8) Wave 5-6 completion + spawn discoverability test. |
| **First-instance pick rationale** | Zero Claude Flow overlap; content is stable and cross-project; the "where is canonical?" problem is the clearest justification for building it; acts as the first real validation of the iq-locker framework on self-authored (not vendored-third-party) content. |
| **Started** | 2026-04-10 |

## Reference implementations (not managed by iq-locker)

These are existing locker-pattern implementations that predate iq-locker or operate outside its scope. They're listed here because the framework derives from or references them.

### T3 Hearst `fabric-toolkit`

| Field | Value |
|-------|-------|
| **Home** | `/Users/kmgdev/dev_projects/t3-hearst/docs/fabric-toolkit/` |
| **Relationship** | The original validated POC of the skill locker pattern (2026-04-10). Predates iq-locker as a framework. Vendors Microsoft's `skills-for-fabric` MIT content with active/dormant curation, a sync script, and a wire script exposing active skills into `.claude/skills/`. The pattern formalized in `SKILL-LOCKER-PATTERN.md` is traceable to this instance. |
| **Used as** | Reference implementation for **Phase 4** of the iq-locker brief — "Extract reusable framework scaffolding." The fabric-toolkit's sync/wire script pair and MANIFEST format are the model for the iq-locker init pattern. |
| **Managed by iq-locker?** | No. Lives in a client project, curated independently, not in the framework's scope. |

## Master-index convention for consumer projects

Any project consuming one or more iq-locker-managed lockers should have a **"Lockers" section** in its top-level `CLAUDE.md` that serves as the project-scoped master index. This is a *different* artifact from this registry — this registry is framework-scoped (all instances wherever they live); the consumer project master index is project-scoped (which lockers matter for tasks *in that project*).

Example entry for the master index in `KGiQ-LLC/CLAUDE.md` once the IQ Design System locker is built:

```markdown
## Lockers

- **IQ Design System** (`iq-design-locker/MANIFEST.md`) — Frontend styling, brand
  colors, typography, components, voice/tone, tech-nerd aesthetic. Use for any UI
  work, marketing copy review, or theme setup in an IQ-branded project.
```

The consumer project's entry points at the locker's MANIFEST (relative path from the project root). The locker's own internal structure is opaque to the master index; the MANIFEST handles routing from there.

## Adding a new instance

1. Complete Phase 0 scoping: pick the first-instance candidate, run the appropriate brand/content audit, produce a design audit doc in `docs/plans/`.
2. Decide where the locker physically lives (consumer project repo, not iq-locker).
3. Add an entry to this registry with status `scoping`.
4. Work through Phases A–D per the SKILL-LOCKER-PATTERN.md conventions + the fabric-toolkit reference implementation.
5. Flip the entry status to `validated` after Phase D passes a spawn-discoverability test.
6. Flip to `live` when the first consumer project is reconciled and in active use.

## Archival policy

Deprecated lockers should be marked `deprecated` in this registry with a reason, not deleted. The design audit, session history, and `LINEAGE.md` reference stay in place as historical record. Only when a locker is physically removed from its consumer project should its entry be archived (moved to a `## Archived` section at the bottom of this file).
