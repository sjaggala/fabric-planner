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

## The eBook — "The Fabric Field Guide" (companion eTextbook)

A **separate, self-contained `ebook.html`** in this same folder/repo — a deep, book-structured
DP-600 study companion that grows one subtopic at a time. Reached from the planner via an **eBook
button** in the header (top-right, next to the Plan/Progress toggle). Same design system as the
planner (fonts, CSS-variable palette, phase colors), plus a full dark mode.

**Why a separate file (decision):** kept out of `index.html` so the planner stays lean while the
book grows large with figures. The eBook uses a three-column reader layout that would fight the
planner's centered layout as an in-page view. One-click navigation between them; both committed to
the same repo. (Confirmed with the user 2026-08-20.)

### Layout & features
- **Three columns:** left = full roadmap/contents (the "tracker"); center = active section (SPA
  swap); right = "On this page" TOC with scrollspy. Collapses to a drawer on mobile.
- **Search** across all section titles + the body text of authored sections (highlighted snippets).
- **Dark-mode toggle** (`◐` / `☀`), **back-to-Planner** link, per-viewer state in localStorage.

### Progressive unlocking (core requirement)
- The sidebar shows the **entire planned outline** (all parts → chapters → sections) as a roadmap,
  with a status glyph per section: ✓ completed · ● current · 🔒 locked.
- Only **completed sections + the single current section** are openable; locked ones are disabled.
- **Linear, prefix-based:** `currentIndex` = number of leading completed sections. Finishing the
  current section (**"Mark complete & continue"**) advances to and unlocks the next. "Mark as not
  done" rolls back and re-locks everything after it, keeping the completed set a contiguous prefix.
- Unlock is **reader-progression driven and additionally gated by authoring**: if the current
  section has no content yet, it shows a "being written" placeholder (still the current step).

### Data model (top of the `ebook.html` IIFE)
- `BOOK` — the full outline: array of Parts → `chapters` → `sections` (`{id,num,title}`). This alone
  drives the sidebar roadmap. **Adding a section to the plan = add an entry here.**
- `CONTENT` — a map keyed by section `id` → authored HTML string. **A section is "authored" iff its
  id is a key here.** Writing a section = add `CONTENT["sX-Y"] = ...` (and, for long bodies, a
  `CONTENT_SX_Y()` builder function near the bottom, mirroring `CONTENT_S1_1`).
- `FLAT` / `META` — derived flatten + lookup. `PHASE_VARS` maps phase → CSS color vars.
- localStorage keys: `fabricEbookProgress_v1` (`{completed:{[id]:"YYYY-MM-DD"}}`),
  `fabricEbookTheme_v1`, `fabricEbookLast_v1` (resume point), `fabricEbookCollapse_v1` (chapter
  fold state).

### Content authoring conventions (match these when writing new sections)
- A section body is an HTML string: `<p class="kicker">`, `<h1>`, `<p class="lede">`, then `<h2>`
  sub-subtopics. **Every `<h2>` carries `data-toc="short label"`** and starts with
  `<span class="h2n">1.1.1</span>` — the right-hand TOC is auto-built from the `<h2>`s.
- **Callouts** (`<div class="cal TYPE">` + `.cico` icon + `.clab` label + `.cbody`): `def` 📘
  Definition · `key` 🔑 Key idea · `exam` 📝 Exam tip (DP-600) · `scn` 🌍 Real-world scenario ·
  `warn` ⚠️ Common misconception/gotcha. Use `scn` liberally — the user learns by real-world tie-in.
- **Tables** wrapped in `<div class="tablewrap">` for horizontal scroll.
- **Figures:** `<figure><div class="figbox">…</div><figcaption><b>Figure N — …</b> …</figcaption></figure>`.
  Diagrams (architecture, flow) = **inline SVG**, theme-aware via `var(--…)` fills, built by a helper
  fn (see `FIG_FABRIC`); data charts = **Python/matplotlib PNG** in `figures/`, referenced by
  relative path. (Figure-style decision confirmed with user 2026-08-20.)
- **References:** a `<div class="refs">` list of **Microsoft Learn** links at the end of each
  section — MS official docs are the source of truth; fact-check every section against current docs
  (Fabric changes fast — mark preview features, note where 2026 differs from older write-ups).
- **Depth:** the user has ~7 yrs Power BI. Write with real technical depth and real-world scenarios;
  known topics can be a quick read but must still go deeper. Not terse — full explanations.

### Tutoring workflow for the eBook (how we grow it)
One subtopic at a time by default: research (MS Learn) → author the section into `CONTENT` → the
reader unlocks it in sequence. Pause for the user to review depth/style before writing more (unless
they say "do the whole chapter at once"). Quizzes/grading, if used, happen in chat, not the eBook.

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

- **2026-08-20** — **Pushed the companion eBook to GitHub Pages.** A prior session had committed
  the eBook locally ("Add companion eBook (The Fabric Field Guide) with Subtopic 1.1") but couldn't
  push. Cleaned stale `.git/` cruft (HEAD.lock, index.lock, `_stale_index_lock_*`, `tmp_obj_*`),
  ran `git gc`, verified with `git fsck` (clean), pushed `origin main`. Confirmed
  https://sjaggala.github.io/fabric-planner/ebook.html returns HTTP 200 and the planner's eBook
  button is live. No content changes this session — working tree was already clean.
- **2026-08-20** — **Started the companion eBook ("The Fabric Field Guide").** Created a new
  self-contained `ebook.html` (three-column reader: roadmap sidebar + content + on-this-page TOC,
  search, dark mode) with the full DP-600 book outline (4 Parts, 8 Chapters, 32 sections) as a
  locked roadmap, and progressive prefix-based unlocking. Authored **Subtopic 1.1 "What is
  Microsoft Fabric?"** in depth, fact-checked against current Microsoft Learn (SaaS positioning,
  OneLake one-per-tenant + Delta Parquet/**Iceberg**, expanded workload list incl.
  Databases/Industry Solutions/**Fabric IQ preview**, Copilot), with an inline-SVG Figure 1.1.
  Added the **eBook button** to `index.html`'s header (gold pill + book icon → `ebook.html`).
  Added this "The eBook" architecture section to CLAUDE.md and an eBook roadmap/status + changelog
  to PLAN.md. Verified headless: 32 sections, locking + unlock flow, both themes, no page errors.
- **2026-08-20** — Published the planner to GitHub Pages. Created public repo
  `sjaggala/fabric-planner`, committed `index.html` + context files, enabled Pages (main root),
  verified https://sjaggala.github.io/fabric-planner/ returns HTTP 200. Updated CLAUDE.md
  (Publishing section, live URL, new-files convention, this log) and PLAN.md (status + changelog).
