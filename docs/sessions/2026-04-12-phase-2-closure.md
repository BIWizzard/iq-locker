# Phase 2 Closure: Wave 5-6 Wiring + Plan Cleanup + Spawn Test — 2026-04-12

**Repo:** iq-locker
**Branch:** `main`
**Phase:** Phase 2 (Build first-instance IQ Design System Locker) — **closed this session**
**Task(s):** Execute Waves 5+6 (Tasks 17, 18, 19) from `docs/plans/iq-design-system-build-plan.md`, with user-directed mid-session pivots into (a) legacy-path cleanup in the build plan and brand audit, (b) stale Brand Standards remediation in `KGiQ-LLC/CLAUDE.md`, and (c) forward-looking gap closure planning after the spawn test surfaced three content gaps.

## Session Summary

Phase 2 is closed. This session executed the final three tasks from the 19-task build plan, passed the empirical spawn discoverability test on the first try, and did several rounds of opportunistic cleanup so nothing stale was left sitting between a decision-making agent and old assumptions. The locker is now `validated` per `docs/lockers-index.md` and Phase 2 in the brief is marked complete with full deliverables. The user's framing for the session was sharp: "I don't want a discovery item to keep popping up saying that we've got dead code" — which drove several cleanups that weren't strictly in Task 17/18/19's literal scope but were necessary for the spawn test to validate a gap-free discovery chain.

The session had three distinct phases. First, a mid-session pivot (well before Task 17) into cleaning up the build plan's legacy paths. The last session had explicitly decided *not* to rewrite historical plans ("preserves historical record") and documented a path resolution table at `LINEAGE.md §7.9`. This session the user overrode that decision after realizing the path resolution lives in a file that agents reading the build plan would never find without already knowing to look. The chosen fix was an "As-Built Amendment" banner at the top of the build plan with 5 numbered deltas (base reversal, 4 accents, `iq-brand-system.md`, logo reorg, guidelines v3.0), a surgical inline note on Task 19, a rewritten global Success Criteria checklist with ✅/⏳ markers, and a parallel "As-Built Pointer" at the top of the sibling brand audit doc. This preserved historical task text while making every decision-relevant delta unmissable.

Second, the actual Wave 5-6 wiring. Task 17 (add a Lockers section to `KGiQ-LLC/CLAUDE.md`) expanded during execution into a full CLAUDE.md cleanup after finding the existing Brand Standards section had stale hex values (`#5E7F9B, #8DA377, #FFD166, #B6BBB6` described as "canonical logo colors") and an incomplete font stack ("Inter for UI, JetBrains Mono for code/accents" — missing Plus Jakarta Sans). Left alone, these would have poisoned the Task 19 spawn test by contradicting the Lockers section. The fix: route Session Context item 3 through the Lockers section, replace Brand Standards with a thin pointer to the locker (implementation) + guidelines.md v3.0 (identity), and leave the rest of the file (Git identity, Commit guidelines, Changelog maintenance) untouched. Task 18 was a straightforward creation of `iq-design-locker/CLAUDE.md` as a mid-tree anchor for agents that arrive inside the locker without walking through the parent CLAUDE.md first.

Third, Task 19's spawn discoverability test. A blank-slate general-purpose subagent was spawned with a concrete design query ("add a new secondary button variant for the KGiQ parent site, explain your discovery process") and asked to trace every file it consulted. The test passed all five success criteria on the first try, and the subagent's own verdict was unusually decisive: *"The routing from CLAUDE.md → MANIFEST.md → docs/iq-brand-system.md is excellent — a fresh agent can land on the source of truth in 3 reads."* Along the way, the subagent surfaced **three content gaps** in the locker itself that didn't break the test but represent dead zones the locker should fill before Phase E consumer migrations. Those gaps were captured in a new forward-looking plan doc (`docs/plans/2026-04-12-locker-gap-closure.md`) and documented in `LINEAGE.md §8` as test-surfaced discoveries with actionable proposed fixes. Session closed with a full post-Phase-2 cleanup wave: status flip in `lockers-index.md`, Phase Tracker completion in `brief.md`, LINEAGE.md §8 added, MANIFEST.md entry points updated, two iq-locker commits pushed, and a massive 134-file catch-up commit pushed to `KGiQ-LLC` (which had never committed any of the 3-session locker build — a framework gap worth noting separately).

## What Was Built/Changed

### `iq-locker` repo (this project)

**New files:**
- `CLAUDE.md` — framework config, committed for the first time (had been untracked since 2026-04-10 project init)
- `.gitignore` — new, excludes Claude Code / Claude Flow runtime files, ruvector.db, editor/OS/log/secrets patterns
- `docs/plans/2026-04-12-locker-gap-closure.md` — forward-looking plan tracking 3 content gaps surfaced by Task 19

**Modified files:**
- `docs/brief.md` — Phase Tracker row 2 marked **complete** with dates `2026-04-10 → 2026-04-12` and full deliverables summary
- `docs/lockers-index.md` — IQ Design System Locker status flipped `scoping` → `validated`; LINEAGE.md entry updated to reflect 8 sections (from "not yet created")
- `docs/plans/iq-design-system-build-plan.md` — 3 distinct edits: (1) top-of-file "AS-BUILT AMENDMENT" banner with 5 numbered deltas pointing at LINEAGE refs, (2) inline "as-built note" inside Task 19 updating success criteria to require `iq-brand-system.md` discoverability plus adding iQ Circle rose to the stray-accent check, (3) rewritten global Success criteria checklist with ✅/⏳ markers, inline strikethroughs, and Wave 5-6 items explicitly flagged
- `docs/plans/iq-design-system-brand-audit.md` — top-of-file "AS-BUILT POINTER" blockquote listing 5 obsolete framings and redirecting readers to build plan amendment + LINEAGE §7 + `iq-brand-system.md`

**Commits (2):**
- `db5d738` chore: add .gitignore and track CLAUDE.md framework config
- `f314411` docs: Phase 2 complete — Wave 5-6 wiring, spawn test, plan cleanup

**Push flushed previously unpushed commits:** `7fcc780`, `21fed33`, `7df3316` (the prior three om-wrap commits). Origin had been at `0b67c37` since project init.

### `KGiQ-LLC` repo

**Modified files:**
- `CLAUDE.md` — added new `## Lockers` section between Session Context and Code Style (front-loaded trigger keywords, 4 active product accents with hex values, `MANIFEST.md` + `iq-brand-system.md` pointers, "do not rely on hand-remembered brand facts" warning); updated Session Context item 3 to route brand work through Lockers; replaced Brand Standards section body with thin pointer to locker + guidelines v3.0
- `.gitignore` — appended `ruvector.db` + `*.db-journal/shm/wal` patterns

**New files:**
- `iq-design-locker/CLAUDE.md` — mid-tree anchor file created this session as Task 18

**Commit (1):**
- `ab863d7` feat: add IQ Design System Locker + branding consolidation + wire — 134 files changed, 6,915 insertions, 367 deletions. This single commit spans 3 sessions of uncommitted work: Waves 1-4 locker build (2026-04-11), the 2026-04-12 consolidation detour (`iq-brand-system.md` synthesis, Circle accent, guidelines v3.0 rewrite, logo reorg, archive directory, cross-app drift report), and this session's Wave 5-6 wiring. Git detected most "deletions" as renames since archived files were byte-identical to originals. Branding/ reorg history stayed clean as a result.

**Push flushed previously unpushed commit:** remote was at `4595b22` (two commits behind the local pre-session HEAD).

### `KGiQ-LLC/iq-design-locker/` (inside KGiQ-LLC repo, part of the ab863d7 commit)

**New files created this session:**
- `CLAUDE.md` — mid-tree anchor (Task 18): opens with what the locker is, points UP to parent CLAUDE.md Lockers section as primary discovery mechanism, enforces invocation protocol, lists 5 canonical entry points, reminds about LINEAGE update requirement, 3 "do not" anti-patterns (bypass MANIFEST, hand-copy vendor, consult `docs/history/` for current decisions — with panel doc exception).

**Modified files this session:**
- `MANIFEST.md` — added new line to "Related entry points" mentioning the mid-tree `CLAUDE.md` anchor; promoted `KGiQ-LLC/CLAUDE.md` to "primary discovery mechanism"
- `LINEAGE.md` — added new **§8 "Wave 5-6 completion — wiring and spawn discoverability test"** (~180 lines): §8.1 Task 17 wiring + CLAUDE.md cleanup, §8.2 Task 18 anchor + MANIFEST update, §8.3 Task 19 full 5-criteria audit trail + 10-file discovery chain + subagent verdict, §8.4 Assumption [A1] validated with direct evidence, §8.5 three content gaps with evidence, §8.6 Phase 2 closure checklist + next phases pointer

## Key Decisions

| Decision | Rationale |
|----------|-----------|
| **Override last session's "don't rewrite historical plans" decision** | Last session chose to preserve the build plan's historical task text and rely on `LINEAGE.md §7.9` path resolution. But LINEAGE lives in the locker; the build plan lives in iq-locker. An agent opening the build plan cold has no pointer to LINEAGE. The path resolution table only protects readers who already know where to look. User explicitly redirected after realizing this: "I don't want a discovery item to keep popping up saying that we've got dead code." |
| **Banner + surgical Task 19 update + rewritten Success criteria, not full rewrite** | Three options were considered: (A) banner + surgical edits [chosen], (B) full rewrite, (C) archive the plan and write a new focused Wave 5-6 execution plan. Option A preserves the historical record of what was originally planned vs. what executed, makes every delta unmissable via the top-of-file banner, and surgically updates only the parts an agent about to execute would read (Task 19 criteria, global Success checklist). Full rewrite would lose the historical record; archive+new would proliferate planning docs. |
| **Also apply the same banner-pattern to the sibling brand audit doc** | `iq-design-system-brand-audit.md` was referenced by the build plan as "read first" and had the same 5 stale framings. A shorter pointer blockquote redirecting to the build plan's amendment + LINEAGE §7 was added — doesn't duplicate the deltas, just ensures a reader of the audit hits the "this is historical" warning before engaging with the content. |
| **Clean up `KGiQ-LLC/CLAUDE.md` Brand Standards section as part of Task 17** | Task 17 literal scope was "add a Lockers section with front-loaded trigger keywords." But the existing file had a Brand Standards section with stale hex values and incomplete font stack (missing Plus Jakarta Sans). Left alone, a Task 19 spawn test subagent reading the file top-down would hit Brand Standards before Lockers and pick up stale facts. The Brand Standards cleanup was a necessary condition for Task 19 to empirically validate a gap-free discovery chain, not scope creep. |
| **Replace Brand Standards body with thin pointer, not delete the section** | Option A: delete entirely. Option B: replace body with pointer to Lockers + guidelines v3.0. Chose B — preserves the section anchor for grepability and preserves the file's existing heading hierarchy. The pointer explicitly says "do not hand-remember brand facts in this file" to prevent regression. |
| **Update Task 19 success criteria inline via "as-built note"** | The original Task 19 criteria required the subagent to read SKILL.md files. Since `iq-brand-system.md` was added 2026-04-12 as the single source of truth, a well-routed subagent should find that too. Rather than rewriting the criteria destructively, added a blockquote "As-built note" explaining the change + updating the affected bullets inline. Keeps the original criteria visible as historical context. |
| **Spawn Task 19 subagent with explicit cwd in KGiQ-LLC + minimal priming** | The Agent tool spawns subagents in the parent session's context by default. For the test to validate KGiQ-LLC/CLAUDE.md auto-loading, the prompt explicitly said "working in /Users/kmgdev/KGiQ-LLC/" and gave only the design task — no mention of "locker", "MANIFEST", or "skill" anywhere. Critical detail: asked the subagent to **explain its discovery process step-by-step** so the success criteria could be verified against the audit trail. |
| **Separate iq-locker commits: hygiene vs work** | `chore: .gitignore + CLAUDE.md` is not this session's Phase 2 work — it's a repo-init catch-up that's been waiting since 2026-04-10. Split into its own commit to keep the Phase 2 completion commit focused. Two commits is cleaner than one bundled. |
| **Single catch-up commit for `KGiQ-LLC` spanning 3 sessions** | KGiQ-LLC had never committed the locker build. Last session's wrap committed iq-locker but not KGiQ-LLC (a framework gap worth tracking separately). A single catch-up commit with a multi-section body message covering all three sessions' work is more honest than trying to retroactively split into 3 commits that represent "what should have been committed each session." Git rename detection kept the branding reorg history clean. |
| **Defer MANIFEST + LINEAGE updates for Task 18 to the Task 19 completion bundle** | Task 18 creates the nested CLAUDE.md. Updating MANIFEST's "Related entry points" and adding a LINEAGE entry for the Task 18 file are adjacent operations but not strictly within Task 18. Bundling them with the Task 19 completion work (which also needed LINEAGE and MANIFEST updates for the spawn test record) produced cleaner commit content than spreading the maintenance edits across multiple steps. |
| **Capture spawn-test-surfaced gaps in a dedicated plan doc, not the discovery log alone** | The user wants to "fill the gaps soon and remove any gray from the locker." The discovery log captures what was learned; a plan doc captures what to do about it. Created `docs/plans/2026-04-12-locker-gap-closure.md` parallel to the existing `2026-04-12-cross-app-drift-report.md` — both are discovered-drift documents feeding forward into focused closure sessions. Each gap has evidence, proposed fix with code/markdown snippets, effort estimate, and dependencies. |

## Discoveries

- **Discovery: `LINEAGE.md §7.9` path resolution coverage was locker-scoped and didn't reach into `iq-locker/docs/plans/` where the build plan lives.**
  - **Impact:** Last session's decision to rely on LINEAGE for path resolution protection was premised on the idea that any reader of the build plan would already know to consult LINEAGE. That turned out to be false — an agent opening the build plan cold has no pointer at all. The "preserves history" framing was correct but incomplete: preserving history is fine, but the mechanism for redirecting readers needs to live in the same doc they're reading, not in a sibling doc in a different repo. Framework pattern: **wherever historical docs are preserved, a pointer banner must live at the top of the historical doc itself** — not elsewhere. Generalizes beyond this session.

- **Discovery: The spawn discoverability test empirically validated Assumption [A1] for the first time with direct evidence.**
  - **Impact:** Prior validation of A1 was indirect (T3 Hearst POC, Claude Flow audit, Claude Code native audit). This session produced a direct, reproducible test: a blank-slate general-purpose subagent with zero prior knowledge found the canonical source of truth in 3 reads, explicitly read SKILL.md binding rules before acting, and refused to use `--iq-accent` for interactive elements (cited §2 token routing rules). The MANIFEST-read protocol works as designed. This is now the gold standard test pattern for any future locker instance.

- **Discovery: Subagents can cite SKILL.md binding rules ("don't invent variants without updating §6") and use them to constrain their own output.**
  - **Impact:** The Task 19 subagent found `iq-components/SKILL.md` said *"The canonical component set is the source of truth — don't invent variants without updating `docs/iq-brand-system.md` section 6."* It then surfaced this as a scope concern, delivered a fully-resolved version of the existing Secondary variant instead of inventing a new one, and flagged in its ambiguity section that "new secondary variant" could mean three different things. This validates that SKILL.md binding rules aren't just documentation — they actively steer subagent behavior. Worth codifying as a framework pattern: *"SKILLs that enforce scope discipline should use explicit binding language that agents can cite."*

- **Discovery: The `parent-layer/iq-site-tokens.css` does not override `--iq-color-fill-secondary` or `--iq-color-fill-tertiary`, creating unresolved ambiguity about whether the KGiQ parent site relies on runtime `data-theme="dark"` setting.**
  - **Impact:** In dark-first context the inherited light-mode fill values (`#F5F5F7`, `#EBEBED`) would glare unacceptably on a slate-900 canvas. Either (a) parent-layer is missing overrides (real bug) or (b) parent-layer implicitly relies on `[data-theme="dark"]` being set at runtime to pull dark-mode values from `core/iq-tokens.css` lines 145-146. Docs alone cannot resolve which. The spawn test subagent couldn't determine it either. This is a Gap 1 action item for the closure session — a 15-minute investigation of how the KGiQ website actually initializes theme resolves it.

- **Discovery: `docs/iq-brand-system.md §6` is thin on concrete button specifics.**
  - **Impact:** §6 defines the button hierarchy (5 variants) and token routing (fill, text, radius, focus ring, press transform) but does not specify padding, font-size, font-weight, hover background, or hover text. The spawn test subagent had to fall back to `docs/history/iq-components-guide.md` (archived/legacy) for values: `padding: 8px 16px`, `font-size: 14px`, `font-weight: 500`. These legacy values map cleanly to canonical tokens (`--iq-space-2`, `--iq-space-4`, `--iq-type-body-emphasis-*`), suggesting a mechanical copy-up is possible. This is a Gap 2 action item: promote the legacy button values into §6 with canonical token routing so future consumers don't need to consult `docs/history/`.

- **Discovery: There may be an unintended "Secondary = Steel Blue" regression between the legacy components guide and canonical §6.**
  - **Impact:** The legacy `iq-components-guide.md` documents Secondary as "Steel Blue `#5E7F9B` + White" which maps to `--iq-tint-base` on the parent site (and would vary by product on Booking/Publish/Circle). Canonical §6 routes Secondary to `--iq-color-fill-secondary` (neutral fill). These are fundamentally different design intents. Either §6 intentionally dropped the tinted pattern (consistent with §2's "interactive elements use `--iq-tint`, NOT `--iq-accent`" rule, possibly favoring neutral fills for hierarchy) or §6 accidentally dropped it during the 2026-04-12 consolidation rewrite. Gap 3 action item: confirm intent with design owner and update §6 with explicit rationale note to prevent future regression.

- **Discovery: KGiQ-LLC had never committed any of the 3-session IQ Design System Locker build — all ~130 files of locker content were sitting untracked in the KGiQ-LLC working tree.**
  - **Impact:** Last session's `/om-wrap` committed to iq-locker (session logs, brief, plans) but not to KGiQ-LLC where the actual locker content lives. The previous session wraps had no mechanism to reach into KGiQ-LLC and commit the content there. This is a framework gap in the `/om-wrap` procedure when locker content and framework memory live in different repos — a multi-repo wrap is needed. For this session, resolved via a single 134-file catch-up commit. For the framework, the `/om-wrap` skill probably needs an enhancement: detect related-repo working-tree state and prompt for commits there too.

- **Discovery: The `iq-circle/iq-design-locker-vendor/VENDOR-PIN` is pinned at commit `72d9a55` which predates the locker existing in KGiQ-LLC's git history.**
  - **Impact:** `72d9a55` is the KGiQ-LLC HEAD that existed at vendoring time (2026-04-12 earlier today) but does not contain the `iq-design-locker/` directory (which was only committed this session in `ab863d7`). So the VENDOR-PIN references a commit that cannot resolve the locker files via `git show`. If anyone tries to re-vendor or audit the pin later, they'll get a "file not found" error. Framework bug. Fix: update `iq-circle/iq-design-locker-vendor/VENDOR-PIN` to the new KGiQ-LLC commit `ab863d7` (or a later commit after gap closure) and add a pre-vendoring check to `vendor-into.sh` that refuses to vendor if the locker isn't committed in its home repo. Separate framework-hardening session.

- **Discovery: Git rename detection kept the massive `branding/` reorganization clean across a single catch-up commit.**
  - **Impact:** When staging 60+ file deletions and additions from the 2026-04-12 branding consolidation (archive/, v1-archive/, logos/exports/, logos/ reorganization), `git status` initially showed everything as separate D + ?? pairs. After `git add -A`, git detected that most "deletions" were byte-identical to the new files and reclassified them as renames (`R`). This preserved blame/history for all the archived files. Worth noting: for content/docs work that involves large reorganizations, staging via `git add -A <dir>` leverages rename detection better than staging individual files by path. Framework pattern for multi-file reorganizations.

## Assumptions Validated/Invalidated

Cross-reference with `docs/brief.md` Assumptions Registry:

- **[A1]** Subagents don't auto-inherit parent-session skill registrations; MANIFEST-read protocol remains necessary. → **validated with direct evidence** (previously indirect). The Task 19 spawn test provides the strongest evidence yet: a blank-slate subagent correctly routed through CLAUDE.md → MANIFEST → source-of-truth in 3 reads, explicitly consulted `iq-components/SKILL.md` binding rules before acting, and produced a fully-traced answer. This is the gold standard for A1 validation.
- **[A3]** Skill locker pattern generalizes from vendored-upstream to self-authored content. → **reinforced**. The locker absorbed Wave 5-6 wiring cleanly, the MANIFEST routing survived expansion to mid-tree anchors (Task 18), and the spawn test worked on first try — all evidence that the three-layer model (core + accents + parent-layer) plus MANIFEST curation layer holds up under maintenance and expansion.
- **[A9]** Native Claude Code SKILL.md format is the correct content format. → **reinforced**. Task 17/18 added two new CLAUDE.md entry-point files (one at the parent level, one at the locker level), both in native markdown format with the same front-loading and anchoring patterns as the existing SKILL.md files. Integrated cleanly with Claude Code's auto-loading behavior.
- **[A11]** Locker lives at `KGiQ-LLC/iq-design-locker/` and IS canonical. → **fully validated**. The spawn test spawned in KGiQ-LLC, discovered the locker via the parent CLAUDE.md Lockers section, navigated into the locker via MANIFEST, and read the canonical implementation reference. The entire canonical-source claim held up empirically.
- **[A12]** `LINEAGE.md` backwards-looking provenance audit is a structural quality gate. → **reinforced with §8 addition**. The new §8 "Wave 5-6 completion" section continues the pattern established in §1-7 and adds a quality-gate-specific record (test result, 5-criteria audit trail, 10-file discovery chain). LINEAGE continues to be the single authoritative source for "what happened to this locker" across sessions. The MANIFEST §6 "When to consult LINEAGE" routing table added last session continues to be the right discoverability mechanism.
- **[A-new from 2026-04-11]** Structural validation is a sufficient substitute for `npm test` in content/docs lockers. → **strongly reinforced**. The Task 19 spawn test IS the structural test — it validates that routing links work, that content is discoverable, that binding rules are enforced, and that token values are traceable to real files. Passed first try with zero escapes. Worth promoting to a first-class iq-locker framework pattern in Phase 4: *"Every locker's quality gate is a spawn discoverability test with a task specific to that locker's domain."*

## Problems & Solutions

| Problem | Resolution |
|---------|------------|
| Build plan referenced legacy paths that would mislead future agents (parent-layer/logos/, old accent count, docs/spec+brief+guide) | Added top-of-file AS-BUILT AMENDMENT banner with 5 numbered deltas each pointing at LINEAGE refs. Preserved original task text. |
| Brand audit doc had the same pre-consolidation framing | Added top-of-file AS-BUILT POINTER blockquote redirecting to build plan amendment + LINEAGE §7. Didn't duplicate the deltas. |
| Task 19 original success criteria didn't mention `iq-brand-system.md` (synthesized after the plan was written) | Added inline as-built note blockquote before the criteria + updated affected bullets to accept either SKILL.md or `iq-brand-system.md` as discoverability evidence. Also added iQ Circle rose to the stray-accent check. |
| Build plan's global Success criteria checklist was stale (3 accents, old docs/ layout) | Rewrote the checklist with ✅/⏳ markers for done vs remaining, inline strikethroughs showing the updates, and Wave 5-6 items explicitly flagged. |
| `KGiQ-LLC/CLAUDE.md` Brand Standards section had stale hex values + incomplete font stack that would contradict Task 17's Lockers section | Replaced Brand Standards body with a thin pointer to Lockers (implementation) + guidelines v3.0 (identity). Kept the section heading for grepability. |
| `KGiQ-LLC/CLAUDE.md` Session Context item 3 pointed readers directly at `branding/guidelines.md`, bypassing the locker | Updated to "see the Lockers section below" routing. |
| `KGiQ-LLC` repo had never committed the 3-session locker build | Single 134-file catch-up commit with a multi-section body covering Waves 1-4 build, 2026-04-12 consolidation detour, and this session's Wave 5-6 wire. Git rename detection kept the branding reorg clean. |
| iq-locker + KGiQ-LLC both had unpushed commits from prior wraps | First push on each branch flushed everything. Both remotes now up to date. |
| Task 19 spawn test surfaced three content gaps in the locker | Captured in a new dedicated plan doc (`docs/plans/2026-04-12-locker-gap-closure.md`) with evidence, proposed fixes, effort estimates. Also recorded in LINEAGE.md §8.5 with cross-reference. |
| Claude Flow daemon died sometime mid-session | Restarted at wrap time before Step 7 (session checkpoint). No CF operations were attempted during the quiet period — main session work was file-based. |

## Open Questions

1. **Gap 1 (parent-layer fill-token gap) must be resolved before any consumer migration.** Is the parent layer missing `--iq-color-fill-secondary` / `--iq-color-fill-tertiary` overrides, or does it rely on runtime `data-theme="dark"` being set? ~15 minutes investigation of how the KGiQ website initializes theme.
2. **Gap 2 (§6 button specifics) should promote legacy values into canonical routing.** Padding, font-size, font-weight, hover rules are missing from `iq-brand-system.md §6` and the subagent had to fall back to archived legacy. Promote to canonical with token routing (`var(--iq-space-2) var(--iq-space-4)`, etc.).
3. **Gap 3 (Secondary = Steel Blue regression) needs a design owner decision.** Is Secondary intended to be tinted (`--iq-tint`) or neutral (`--iq-color-fill-secondary`)? Current §6 says neutral; legacy guide says tinted. Same ambiguity in `iq-circle` front-end work currently in progress.
4. **`iq-circle/iq-design-locker-vendor/VENDOR-PIN` is pinned at a commit that doesn't contain the locker.** Framework bug. Should be updated to `ab863d7` or a post-gap-closure commit. Also, `vendor-into.sh` should gain a pre-vendor check refusing to vendor if the locker isn't committed.
5. **`/om-wrap` does not reach into sibling repos to commit locker content that lives there.** Last session's wrap committed to iq-locker but left the entire 3-session locker build uncommitted in KGiQ-LLC. This session's catch-up fixed it, but the pattern will re-emerge for future lockers (e.g., if we build one for `t3-hearst/docs/fabric-toolkit/`). Framework question: should `/om-wrap` detect working-tree state in project-related external repos and prompt? Or should each locker instance declare its "write target" repo and wrap iterates over all of them?
6. **Should the `iq-locker` framework codify "every historical doc preserved in place must carry a top-of-file pointer"?** This session's build-plan + brand-audit cleanup worked well. If other lockers end up with similar historical preservation needs, the pattern should be extracted. Phase 4 candidate.
7. **Should the Task 19 spawn discoverability test pattern be codified as a first-class quality gate for every locker instance?** The test was empirically decisive and took ~2 minutes of subagent time. Every future locker should probably have to pass one. Phase 4 candidate.

## Tracked Files Changed

Files in the `tracked_files` list from `docs/open-mind.yaml` that were created or updated this session:

- `CLAUDE.md` — first-time tracked (had been untracked since project init; committed in `db5d738` chore commit)
- `docs/brief.md` — Phase Tracker row 2 → complete; assumption statuses unchanged (all reinforcing, no invalidations)
- `docs/plans/iq-design-system-build-plan.md` — AS-BUILT AMENDMENT banner + Task 19 inline note + rewritten Success criteria checklist
- `docs/plans/iq-design-system-brand-audit.md` — AS-BUILT POINTER blockquote at top
- `docs/plans/2026-04-12-locker-gap-closure.md` — new forward-looking plan doc
- `docs/sessions/2026-04-12-phase-2-closure.md` — this file
- `docs/discovery-log.md` — will be appended in Step 5

NOT in `tracked_files` but changed this session:
- `docs/lockers-index.md` — status flip + LINEAGE summary update (not listed in tracked_files but should be — minor open-mind.yaml update candidate)
- `.gitignore` — new

Changed outside this repo (expected, not tracked by iq-locker):
- `/Users/kmgdev/KGiQ-LLC/CLAUDE.md` — Lockers section added + Brand Standards cleanup + Session Context routing update
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/CLAUDE.md` — new mid-tree anchor (Task 18)
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/MANIFEST.md` — Related entry points updated
- `/Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md` — §8 "Wave 5-6 completion" section added (~180 lines)
- `/Users/kmgdev/KGiQ-LLC/.gitignore` — ruvector.db + db-journal/shm/wal patterns appended
- `/Users/kmgdev/KGiQ-LLC/` — single 134-file catch-up commit `ab863d7` covering this session's wiring + two prior sessions' uncommitted locker build work

## Next Steps

1. **Wrap and commit this session.** (In progress — this is `/om-wrap`.)
2. **Next session — close the 3 locker gaps.** Single focused session on the gap-closure plan. Estimated 1-1.5 hours: Gap 1 investigation (~15 min) + Gap 2 §6 rewrite with token routing (~30-45 min) + Gap 3 design decision + implementation (~15 min) + LINEAGE §8.5 resolution updates + re-run the spawn test to verify gap-free discovery chain.
3. **After gap closure — Phase E consumer migrations.** Order per `LINEAGE §3.4`: `iq-publish` (S effort — `--iq-color-info` fix + font loading) → `booking-module` (M — calendar badge tokenization + glass policy review) → `portfolio-front-end` (L — the visible UI shift on the embedded booking widget). Each is its own session. Full catalog in `docs/plans/2026-04-12-cross-app-drift-report.md`.
4. **Framework hardening (Phase 4 candidate, not urgent).** Fix iq-circle's VENDOR-PIN to reference a commit that actually contains the locker. Add pre-vendor check to `vendor-into.sh` refusing vendoring if locker isn't committed upstream. Enhance `/om-wrap` to detect working-tree state in related external repos. Codify the spawn-discoverability-test-as-quality-gate pattern. Codify the top-of-file-pointer-on-preserved-historical-docs pattern.
5. **Flip `docs/lockers-index.md` status from `validated` → `live`** after the first Phase E consumer migration ships cleanly.

## Continuation Prompt

> Continue Phase 2→E bridge: close the 3 locker gaps before starting Phase E consumer migrations, in iq-locker.
>
> **Last session recap:** Phase 2 closed. Wave 5-6 executed (Tasks 17, 18, 19): `KGiQ-LLC/CLAUDE.md` Lockers section wired with trigger keywords + 4 accents + `iq-brand-system.md` pointer, `iq-design-locker/CLAUDE.md` mid-tree anchor created, empirical spawn discoverability test passed first try with a blank-slate subagent (5/5 criteria, 3-read discovery chain, correct token routing). Also did opportunistic cleanup: added "AS-BUILT AMENDMENT" banner to `docs/plans/iq-design-system-build-plan.md` with 5 numbered deltas, added "AS-BUILT POINTER" to the sibling brand audit, rewrote Success criteria checklist with ✅/⏳ markers, replaced stale `KGiQ-LLC/CLAUDE.md` Brand Standards section with thin pointer to locker, added `LINEAGE.md §8` with full spawn test audit trail + 3 content gaps, updated `MANIFEST.md` entry points, flipped `lockers-index.md` to `validated`, marked Phase 2 complete in brief. Committed across iq-locker (2 commits) and KGiQ-LLC (1 big 134-file catch-up commit spanning 3 sessions of uncommitted locker build). Both remotes pushed.
>
> **Priority:** Execute the gap closure plan at `docs/plans/2026-04-12-locker-gap-closure.md`. 3 gaps surfaced by Task 19's spawn test:
> 1. **Gap 1 — Parent-layer fill-token override gap.** `parent-layer/iq-site-tokens.css` doesn't override `--iq-color-fill-secondary` / `--iq-color-fill-tertiary`, which would glare unacceptably in dark-first context. Either a real bug (needs override) or an implicit assumption that `data-theme="dark"` is set at runtime. ~15 min investigation of how the KGiQ parent site actually initializes theme — likely read `/Users/kmgdev/KGiQ-LLC/website/` source or check `portfolio-front-end/public/vendor/` for the vendored parent layer. Then either add overrides OR add a header comment documenting the runtime-theme assumption.
> 2. **Gap 2 — `docs/iq-brand-system.md §6` is thin on concrete button specifics.** Missing padding, font-size, font-weight, hover background, hover text for all 5 variants. Spawn test subagent had to fall back to `docs/history/iq-components-guide.md` (archived/legacy) for values (`8px 16px` padding, `500` weight, `14px` font-size). Legacy values map cleanly to canonical tokens. Promote to `iq-brand-system.md §6` as canonical rules. ~30-45 min with design review on hover rules.
> 3. **Gap 3 — Possible "Secondary = Steel Blue" regression.** Legacy guide says Secondary = Steel Blue `#5E7F9B` (maps to `--iq-tint-base`). Current canonical §6 says Secondary uses `--iq-color-fill-secondary` (neutral). Either intentional scope split (supported by §2's "interactive = tint, not accent" rule) or accidental drop during 2026-04-12 consolidation rewrite. Decide with design owner; update §6 with explicit rationale note.
>
> After the 3 fixes: **re-run the spawn discoverability test** with a new subagent using the same button-variant prompt. Success criterion: subagent should NOT need to fall back to `docs/history/iq-components-guide.md` for any value. If that holds, update `LINEAGE.md §8.5` to mark each gap closed.
>
> **Key context:**
> - Locker is `validated` as of 2026-04-12. Phase 2 is formally complete in `docs/brief.md`. All changes committed + pushed in both repos.
> - **Three content gaps are Phase-blocking for Phase E.** Do NOT start consumer migrations until the 3 gaps are closed — otherwise iq-publish/booking-module/portfolio-front-end would inherit the ambiguity. Closure should happen in a single focused session before Phase E kicks off.
> - `iq-circle` front-end work is in progress by the user. Gap 3 (Secondary tinted vs neutral) directly affects iq-circle's implementation decisions — worth checking how iq-circle is currently rendering Secondary before finalizing the §6 update.
> - `iq-circle/iq-design-locker-vendor/VENDOR-PIN` is pinned at `72d9a55`, a KGiQ-LLC commit that predates the locker being in git. **Update the VENDOR-PIN to `ab863d7` or a later commit** as part of the gap closure session or as a separate framework-hardening task. Also worth adding a pre-vendor commit check to `vendor-into.sh`.
> - **Framework wrap gap:** `/om-wrap` in iq-locker doesn't reach into KGiQ-LLC to commit locker content. This session fixed the backlog via a 134-file catch-up, but the pattern will re-emerge. Phase 4 candidate: enhance `/om-wrap` to detect working-tree state in related external repos and prompt.
> - **Phase E still gated on gap closure.** Consumer migration order per `LINEAGE §3.4`: iq-publish (S) → booking-module (M) → portfolio-front-end (L). See `docs/plans/2026-04-12-cross-app-drift-report.md` for the full catalog.
> - Quality-gate policy still applies: fix all review suggestions and re-review until clean. Don't defer minor findings.
>
> **Brief:** `docs/brief.md` (iq-locker framework philosophy; Phase 2 now shows complete 2026-04-10 → 2026-04-12).
> **Gap closure plan:** `docs/plans/2026-04-12-locker-gap-closure.md` (3 gaps with evidence, proposed fixes, effort estimates, closure checklist).
> **Locker LINEAGE:** `/Users/kmgdev/KGiQ-LLC/iq-design-locker/LINEAGE.md` §8 (Wave 5-6 completion + spawn test + gap discoveries). §7.9 has the path resolution table for anyone reading §1-6 with stale paths.
