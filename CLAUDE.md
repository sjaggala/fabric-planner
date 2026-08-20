# CLAUDE.md — Fabric Planner project context

> This file is read automatically by Claude Code. It is the single source of truth for
> how this project is built and why. **Keep it current** (see "Maintenance rule" at the bottom).

---

## What this project is

A self-contained, single-file web app that acts as an **8-week Microsoft Fabric study planner**
for the **DP-600 (Fabric Analytics Engineer)** certification. It is a personal learning tool for
one user (Sravan), with a day-by-day plan view and a progress-tracking view.

- **Live:** published on GitHub Pages — https://sjaggala.github.io/fabric-planner/ — reachable
  from any device, anytime. (See "Publishing" below for the repo and update workflow.)
- **The whole app is one file:** `index.html` (was `fabric-planner.html` before publishing).

## Who it's for

- **User:** Sravan — ~7 years of Power BI experience (DAX, Power Query/M, dataflows, SQL Server,
  T-SQL, tenant admin, RLS, REST APIs, Azure DevOps, CI/CD for Power BI).
- **Objective:** reach an *intermediate* working knowledge of Fabric and pass **DP-600** in ~2 months.
- **Study budget:** 1 hour/day, 5 days/week (weekends are buffer/catch-up).
- **Python level:** can read and understand code, but does not author Python/PySpark from scratch.
  Labs are designed to be copy-and-adapt, not write-from-zero.

## Tech stack & hard constraints

- **Pure static front-end:** HTML + CSS + vanilla JavaScript. **No framework, no build step,
  no bundler, no npm dependencies.** Do not introduce React, Vue, Tailwind, a build tool, or a
  package.json unless the user explicitly asks. The value of this project is that it's one file
  you can open with a double-click.
- **Fonts:** Google Fonts — `Space Grotesk` (display), `Inter` (body), `JetBrains Mono` (mono/code/dates).
  Loaded via `<link>` in the head. Everything else is system-safe.
- **Persistence:** browser `localStorage` only. No backend, no database, no cookies.
- **Offline-friendly:** after first load (which fetches fonts), it works without internet.

## Architecture of `index.html`

Everything lives in one file: `<style>` block, HTML skeleton, then one `<script>`.

### Data model (top of the script)
- `PHASES` — object, 4 phases, each with `name`, `color` (CSS var), `soft`, `ink`, `blurb`.
- `WEEK_TITLES` — object mapping week number → title string.
- `phaseOf(w)` — maps week (1–8) to phase (1–4): weeks 1–2→P1, 3–4→P2, 5–6→P3, 7–8→P4.
- `DAYS` — the core array. **40 objects**, one per study day (8 weeks × 5 days). Each day object:
  ```
  { w, d, type, title, sub, coverLabel, cover:[...], res:[...], code:[{l,c}]?, tip:{b,t} }
  ```
  - `type` is one of: `learn` | `lab` | `review` | `exam`.
  - `code` is optional (present mainly in notebook/SQL lab days).
  - `cover` items and `tip.t` may contain inline HTML (`<code>`, `<em>`).

### State & storage
- localStorage keys (versioned — bump the `_v1` suffix if the shape changes):
  - `fabricPlannerDone_v1` — object `{ [taskId]: { done:true, on:"YYYY-MM-DD" } }`
  - `fabricPlannerStart_v1` — ISO date string, the plan's start date
  - `fabricPlannerWeek_v1` — last-viewed week number
- `taskId` format: `id(w,d)` → `"w{W}d{D}"` (e.g. `w3d2`).

### Date scheduling
- `schedDate(w,d)` = `startDate + ((w-1)*7 + (d-1))` days.
- If `startDate` is a **Monday**, days land Mon–Fri and weekends stay empty as buffer. The date
  input hint tells the user to pick a Monday. `defaultMonday()` rolls forward to the next Monday.

### Views (toggled, not routed)
- **Plan view** (`#planView`): week rail → filter chips (type) + "Hide completed" toggle →
  phase banner → list of expandable day cards. Expand-all / collapse-all supported.
- **Progress view** (`#progressView`): big completion ring, stat cards (completed / remaining /
  due-by-today / total), per-week progress bars, a 40-cell grid (tap a cell to check a day off),
  a "Next up" pointer to the first incomplete day, start-date picker, and reset button.

### Key render functions
`renderRail`, `renderBanner`, `renderDays` (plan) · `renderMini`, `renderProgress` (progress) ·
`toggleDone(taskId)` writes state then `refreshAll()` re-renders whatever view is visible.

## Conventions to follow when editing

- Keep it **one file**. If a change is big enough to tempt you to split files, ask the user first.
- **Any new files belong in this `Fabric Planner` folder** (and, if part of the app, get committed
  to the `sjaggala/fabric-planner` repo). Don't scatter project files elsewhere.
- Reuse the existing CSS variables and phase color system; don't hardcode hex values in new UI.
- Watch CSS selector specificity — the stylesheet mixes type-based and element-based selectors;
  avoid rules that silently cancel each other (especially padding/margin between sections).
- Preserve accessibility: visible keyboard focus, `aria-expanded` on collapsibles,
  `prefers-reduced-motion` honored. Don't regress these.
- Content edits (new resources, corrected module names) go in the `DAYS` array, not the markup.
- Test mentally against `file://` — it must work opened directly, not just via a server.

## Known limitations / open caveats

1. **Progress does not sync across devices.** localStorage is per-browser, per-device. A shared
   GitHub Pages URL makes the *page* reachable everywhere, but check-ins won't follow the user
   from laptop to phone. Cross-device sync is the #1 roadmap item (see PLAN.md).
2. **Resource links need verification.** Some MS Learn module titles and YouTube video references
   in `DAYS` were written from early-2026 knowledge and may be renamed/moved. They should be
   verified against current Microsoft Learn before relying on them.
3. **GitHub Pages requires a public repo** on the free tier. The planner contains no secrets, so
   public is fine — but confirm with the user before making anything public.

## Publishing (GitHub Pages) — LIVE

- **Repo:** [`sjaggala/fabric-planner`](https://github.com/sjaggala/fabric-planner) (public).
- **Live URL:** https://sjaggala.github.io/fabric-planner/ (verified HTTP 200, serves `index.html`).
- **Config:** GitHub Pages, source = **main branch, root (`/`)**. Entry file is `index.html`.
- **Workflow for updates:** edit `index.html` in this folder → `git add -A && git commit` →
  `git push`. Pages rebuilds automatically (usually live within ~1 min). Same setup as the
  gym-routine site. Public repo is required for free Pages; the planner holds no secrets.
- The project context files (`CLAUDE.md`, `PLAN.md`) are committed to the repo alongside the app.

---

## Maintenance rule (important)

**These memory files must stay in sync with the project.** Whenever you make a change that affects
architecture, decisions, conventions, or scope:

1. Update **CLAUDE.md** (this file) — reflect the new reality of *how it's built and why*.
2. Update **PLAN.md** — move completed items to "Done", adjust the roadmap, log the decision in
   the changelog with today's date.

Do this **in the same change/commit** as the code edit, not later. Treat outdated context files as
a bug. If the user asks for something that contradicts a decision recorded here, surface the
conflict before proceeding.

---

## Claude self-update log

> A running log of changes **Claude** makes to this project and its context files. This is the
> section to append to whenever you (Claude) act on this project. Keep newest at the top; one entry
> per working session. Detailed decision history still lives in PLAN.md's changelog — this is the
> quick "what did Claude touch" trail.

- **2026-08-20** — Published the planner to GitHub Pages. Created public repo
  `sjaggala/fabric-planner`, committed `index.html` + context files, enabled Pages (main root),
  verified https://sjaggala.github.io/fabric-planner/ returns HTTP 200. Updated CLAUDE.md
  (Publishing section, live URL, new-files convention, this log) and PLAN.md (status + changelog).
