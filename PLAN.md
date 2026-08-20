# PLAN.md — Fabric Planner decisions & roadmap

> Companion to CLAUDE.md. This file holds the *why* behind the plan, the current status, and
> what's next. Update it alongside every meaningful change (see the changelog at the bottom).

---

## The learning plan (what the app teaches)

An 8-week, 40-day path to DP-600, organized into 4 phases. Structured around a running
**medallion architecture** (Bronze → Silver → Gold) built up in the user's Fabric trial tenant,
so each week's labs feed the next.

| Phase | Weeks | Focus |
|------|-------|-------|
| **1 — Foundation** | 1–2 | OneLake, Lakehouse, Delta Lake, notebook orientation |
| **2 — Data Engineering** | 3–4 | PySpark transforms, Data Factory pipelines, Dataflows Gen2 |
| **3 — Warehouse & Power BI** | 5–6 | Fabric Warehouse + T-SQL, Direct Lake, semantic models, ALM |
| **4 — Governance & Exam** | 7–8 | Admin, security, Purview governance, DP-600 practice tests |

### Module priority (decided with the user)
- **Heavy focus:** Data Engineering (notebooks), Data Warehouse, OneLake & Lakehouse.
  *(These match what's most expected in the user's work.)*
- **Moderate focus:** Data Factory (pipelines + Dataflows Gen2), Power BI in Fabric (new concepts
  only — Direct Lake, semantic models, Fabric-native deployment).
- **Light touch:** Administration & Governance.
- **Explicitly excluded:** Real-Time Intelligence (KQL/streaming) and Data Science (ML). Out of
  scope for this run; revisit only if goals change.

### Certification
- Target: **DP-600 (Fabric Analytics Engineer)** — chosen because it covers the full analytics
  stack and leans on the user's existing Power BI depth. (DP-700 was considered and set aside as
  more code/pipeline-heavy than needed.)

## Key product decisions (the "why")

- **Single self-contained HTML file.** Chosen so the planner opens with a double-click, needs no
  install, and works offline. This constraint is deliberate — protect it.
- **localStorage for check-ins.** Zero-infrastructure persistence that fits a personal tool. The
  known trade-off is no cross-device sync (see roadmap).
- **Two views, not a router.** Plan vs Progress are toggled in-page to keep everything in one file
  and one screen context.
- **Design identity:** technical/product feel via Space Grotesk + Inter + JetBrains Mono and a
  phase color system, deliberately avoiding the generic cream-serif-terracotta template look.
- **Start-date driven schedule.** The user sets a Monday start; the app maps 40 study days onto
  weekdays and leaves weekends as buffer, matching the 5-days/week budget.
- **Labs are copy-and-adapt.** Given the user's read-but-don't-author Python level, notebook/SQL
  labs ship real code blocks to modify rather than asking them to write from scratch.

## Current status

- ✅ Full 8-week / 40-day plan authored and built into `index.html`.
- ✅ Plan view: week rail, type filters, hide-completed, expand/collapse, per-day resources +
  code + tips.
- ✅ Progress view: completion ring, stat cards, per-week bars, 40-cell check-in grid, "Next up",
  start-date picker, reset.
- ✅ Persistence via localStorage; pace indicator ("on pace" vs "behind").
- 🔜 **Not yet published** — next action is GitHub Pages deployment (rename to `index.html`,
  public repo, serve from main root).

## Roadmap / future work (rough priority order)

1. **Publish to GitHub Pages.** Public repo, `index.html` at root. Confirm the live URL returns 200.
2. **Cross-device progress sync.** The #1 enhancement. Options, simplest → most involved:
   - Manual **export/import** of progress as a JSON file (quick win, no accounts).
   - Sync to a **GitHub Gist** via a personal access token (works with the Pages setup).
   - A tiny backend / hosted KV store (only if the above prove insufficient).
   Decide the approach with the user before building.
3. **Verify & refresh resource links.** Confirm current MS Learn module titles and swap in real,
   working links; sanity-check the YouTube references. (See caveat in CLAUDE.md.)
4. **Exam-date countdown.** Let the user set a target exam date and show days remaining + whether
   the current pace lands before it.
5. **Per-day notes.** A small free-text field per day (stored in localStorage) for the user's own
   observations and "still unclear" items.
6. **Nice-to-haves:** dark mode; a print/export-to-PDF view of the full plan; keyboard navigation
   between days.

## Open questions to revisit with the user

- Public vs private repo (public required for free Pages) — confirm before publishing.
- Which cross-device sync approach fits their comfort level (JSON export vs Gist vs backend)?
- Do they want the planner at the repo subpath or at their `<username>.github.io` root?

---

## Changelog

Keep newest at the top. Log every meaningful decision or change with a date.

- **2026-08-20** — Created CLAUDE.md and PLAN.md as project memory files. Captured architecture,
  module priorities, certification choice (DP-600), key decisions, known limitations, and roadmap.
  Project is feature-complete for v1 and ready to publish to GitHub Pages (pending rename to
  `index.html` and public repo).
- **2026-08-20** — Built the complete planner in a single `fabric-planner.html`: full 40-day plan,
  Plan + Progress views, localStorage check-ins, start-date scheduling.
