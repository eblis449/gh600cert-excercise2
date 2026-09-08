# Project Pulse Dashboard — Implementation Plan

## 1. Summary

Project Pulse is a small, runnable static web app that gives Mona's contributors
an at-a-glance dashboard of active projects: name, owner, status, recent
activity, and priority/risk. It lives under `app/` and is previewed from a
VS Code launch configuration named **Run Project Pulse Dashboard** that serves
the `app/` directory and opens `index.html` in a browser (never a directory
listing).

End state:

- `app/index.html` renders a titled dashboard containing project cards driven
  by `app/project-data.json`.
- `app/styles.css` supplies a polished, responsive card UI with the required
  hooks `.dashboard` and `.project-card`, plus `border-radius` and `box-shadow`.
- `app/project-data.json` provides a top-level `projects` array with `name`,
  `owner`, `status`, `recentActivity`, and `priority` on each entry.
- `.vscode/launch.json` is strict JSON (no comments), `cwd` =
  `${workspaceFolder}/app`, launches `python3 -m http.server 5500`, and uses a
  `serverReadyAction` to open `http://localhost:%s/index.html`.

## 2. Ordered implementation steps

1. **Agree the data schema** for `app/project-data.json` (top-level `projects`
   array; required fields; enumerated values for `status` and `priority`).
2. **Scaffold `app/index.html`** with the `Project Pulse` title, the
   `.dashboard` container, the empty card grid region, links to `styles.css`,
   and the JS loader for `project-data.json` (via `fetch`, with graceful
   fallback messaging).
3. **Draft `app/styles.css`** targeting the DOM hooks defined in step 2,
   including `.dashboard`, `.project-card`, status badge classes, priority
   treatments, `border-radius`, `box-shadow`, and a responsive grid.
4. **Populate `app/project-data.json`** with 4–6 realistic seed projects that
   exercise every status/priority variant and at least one long title.
5. **Create `.vscode/launch.json`** as strict JSON with the
   **Run Project Pulse Dashboard** configuration.
6. **Integrate & validate**: run the launch config, confirm cards render from
   JSON, styles apply, and the layout is responsive.

## 3. File assignments

| File | Owner | Purpose | Key requirements |
|---|---|---|---|
| `app/index.html` | Coder | Dashboard markup, data loading, card rendering | Exact `<title>` and visible heading `Project Pulse`; links `styles.css`; references and fetches `project-data.json`; root `.dashboard` element; renders each project as `.project-card` showing `name`, `owner`, `status`, `recentActivity`, `priority`; semantic, accessible markup (landmarks, headings, `aria-live` for status region, alt text where needed) |
| `app/styles.css` | Designer | Visual system and responsive layout | Must define `.dashboard` and `.project-card` selectors; include `border-radius` and `box-shadow`; status badges (`.badge--on-track`, `.badge--at-risk`, `.badge--blocked`, `.badge--done`); priority accents (`.priority--low/medium/high/critical`); readable typography, spacing scale; responsive grid (single column at ≤600px, multi-column at larger widths); focus states and WCAG AA contrast |
| `app/project-data.json` | Coder | Seed data | Strict JSON; top-level `"projects"` array; each project object contains `name`, `owner`, `status`, `recentActivity`, `priority` (plus optional `progress`, `dueDate`, `summary`); enumerated `status` and `priority` values agreed in step 1 |
| `.vscode/launch.json` | Coder | Runnable preview | Strict JSON with no comments; configuration named exactly `Run Project Pulse Dashboard`; `cwd` = `${workspaceFolder}/app`; runs `python3 -m http.server 5500`; `serverReadyAction` opens `http://localhost:%s/index.html`; deterministic port and URL |

## 4. Designer responsibilities

**Scope:** `app/styles.css` only.

**Deliverables:**

- Polished dashboard visual system: header, card grid, project card, status
  badge, priority indicator, empty-state, and error-state styles.
- Responsive layout: single column ≤600px, 2 columns ~600–960px, 3+ columns
  above; consistent gutters and max content width.
- Accessible defaults: WCAG AA color contrast for text and badges, visible
  `:focus-visible` outlines, no reliance on color alone for status/priority
  (also convey via label text and/or icon).

**Constraints:**

- Must include the exact selectors `.dashboard` and `.project-card`.
- Must include `border-radius` and `box-shadow` declarations.
- Must not modify HTML or JSON files.
- Style hooks must match the class names the Coder places in `index.html`
  (see Dependencies, section 6).
- No external CSS frameworks or network assets; system font stack is fine.

## 5. Coder responsibilities

**Scope:** `app/index.html`, `app/project-data.json`, `.vscode/launch.json`.

**Deliverables:**

- `app/index.html` with:
  - `<title>Project Pulse</title>` and a visible `Project Pulse` heading.
  - `<link rel="stylesheet" href="styles.css">`.
  - A root container `<main class="dashboard">` (or equivalent) and a card
    container the CSS targets.
  - Inline `<script>` that `fetch('./project-data.json')`s the data, iterates
    `data.projects`, and renders one `.project-card` per project, displaying
    `name`, `owner`, `status`, `recentActivity`, and `priority`.
  - Graceful empty state ("No projects yet") and error state ("Could not load
    project data") messages when fetch fails or returns no entries.
  - Comment in the HTML referencing `project-data.json` so the file is
    discoverably linked even for readers who don't run JS.
- `app/project-data.json`: valid JSON, top-level `projects` array with 4–6
  seed entries covering the full status/priority matrix and one intentionally
  long name.
- `.vscode/launch.json`: strict JSON, no comments, no trailing commas;
  configuration named exactly `Run Project Pulse Dashboard`; `cwd` set to
  `${workspaceFolder}/app`; command `python3 -m http.server 5500`;
  `serverReadyAction` pattern opens `http://localhost:%s/index.html`.

**Constraints:**

- Do not modify `styles.css`.
- Deterministic behavior: fixed port `5500`, fixed URL path `/index.html`,
  fixed configuration name.
- Keep JS minimal, dependency-free, and explicit; escape any text inserted
  into the DOM to prevent HTML injection from JSON.
- Match existing repo patterns (no build tooling; plain files under `app/`).

## 6. Dependencies between steps

- Step 1 (schema agreement) blocks steps 2 and 4: `index.html` renders and
  `project-data.json` populates the same field names, statuses, and priorities.
- Step 2 (`index.html` DOM/class contract) blocks step 3 (`styles.css`): the
  Designer must know the exact class names to target (`.dashboard`,
  `.project-card`, badge classes, priority classes, card sub-element classes
  such as `.project-card__title`, `.project-card__meta`, `.project-card__activity`).
- Step 5 (`.vscode/launch.json`) depends only on the existence of `index.html`
  at `app/index.html` and the chosen port; it does not depend on styling or
  data content.
- Step 6 (integration & validation) depends on all prior steps.

To keep step 3 unblocked, the Planner records the agreed CSS-hook contract in
this document (see Appendix A) before Designer starts.

## 7. Parallel vs. sequential work

**Sequential (must be in order):**

1. Schema + CSS-hook contract agreement (this plan).
2. Coder scaffolds `app/index.html` skeleton exposing the agreed hooks.
3. Once the hook contract is frozen, Designer and Coder proceed in parallel.
4. Final integration & validation.

**Parallel (safe to run concurrently once the contract is frozen):**

- Designer authors `app/styles.css`.
- Coder authors `app/project-data.json` and `.vscode/launch.json`, and fills
  in the `index.html` render logic against the frozen hooks.

**Never parallel:** any two agents editing the same file. Designer must not
edit `index.html`, `project-data.json`, or `launch.json`; Coder must not edit
`styles.css`.

## 8. Edge cases to handle

- **Empty `projects` array** → render a friendly empty state; do not throw.
- **Missing optional fields** (`progress`, `dueDate`, `summary`) → omit the
  DOM node rather than showing "undefined".
- **Missing required fields** (`name`, `owner`, `status`, `recentActivity`,
  `priority`) → render placeholder text ("Unknown owner", etc.) and continue.
- **Unknown `status` or `priority` value** → fall back to a neutral badge
  class (e.g., `.badge--unknown`, `.priority--unknown`) rather than breaking
  styling.
- **Very long project names or activity strings** → CSS handles wrapping/
  truncation (`overflow-wrap: anywhere`, or a clamp with a tooltip via
  `title`).
- **Many projects (20+)** → grid remains readable; no fixed heights that
  overflow.
- **`fetch` fails under `file://`** → if the learner opens `index.html`
  directly without the launch config, JS fetch of a local JSON file will
  fail; show a clear error state instructing them to use the
  **Run Project Pulse Dashboard** launch configuration. (The launch config
  itself avoids this by serving via `python3 -m http.server`.)
- **Port 5500 already in use** → documented risk; deterministic port is
  required by the exercise. Note it in the empty/error state help text.
- **Invalid JSON in `project-data.json`** → error state message; validated
  during step 6.
- **XSS from JSON strings** → render via `textContent`, never `innerHTML`,
  when injecting project fields.
- **Accessibility** → cards use semantic elements or ARIA roles; badges
  convey status through text, not color alone; focusable elements have
  visible focus.

## 9. Validation expectations

Manual checks (all must pass):

1. `.vscode/launch.json` parses as strict JSON:
   `python3 -m json.tool .vscode/launch.json` exits 0.
2. `app/project-data.json` parses as strict JSON:
   `python3 -m json.tool app/project-data.json` exits 0.
3. Selecting **Run Project Pulse Dashboard** from Run and Debug starts the
   server, and the browser opens `http://localhost:5500/index.html` — showing
   the Project Pulse UI, not a directory listing.
4. The page shows the title/heading `Project Pulse`.
5. At least one `.project-card` is present in the rendered DOM, one per entry
   in `projects`.
6. Each card shows the project's `name`, `owner`, `status`, `recentActivity`,
   and `priority`.
7. Computed styles show `border-radius` and `box-shadow` applied to
   `.project-card`; `.dashboard` layout is a responsive grid.
8. Resizing the window to ≤600px collapses to a single column; wider viewports
   show multiple columns.
9. Keyboard focus is visible on interactive elements; badges are legible
   (contrast).
10. `.vscode/launch.json` contains no `//` or `/* */` comments and no
    trailing commas.
11. `.vscode/launch.json` `cwd` is exactly `${workspaceFolder}/app` and the
    URL pattern is exactly `http://localhost:%s/index.html`.

Repository-level: `bash scripts/validate-exercise.sh` continues to pass (this
plan does not affect any tracked template files).

## 10. Open questions / assumptions

**Assumptions:**

- Python 3 is available in the Codespace (used by the launch command).
- No build step, no framework, no package manager — plain HTML/CSS/JS.
- Fixed port `5500` is acceptable; if in use the learner stops the other
  process.
- The learner previews via the launch config (not by double-clicking
  `index.html`), so `fetch('./project-data.json')` works.
- 4–6 seed projects is sufficient to demonstrate all status/priority variants.

**Open questions:**

- Do we want a client-side filter/sort control (by status or priority) in
  v1, or defer? Default assumption: defer; keep v1 read-only.
- Should `progress` render as a numeric percent or as a progress bar?
  Default assumption: text percent + subtle bar via CSS if trivially cheap.
- Should `dueDate` be shown relative ("in 3 days") or absolute (ISO date)?
  Default assumption: absolute ISO date to keep rendering deterministic.
- Any brand colors from Mona's team to match? Default assumption: neutral
  palette with semantic status colors chosen for AA contrast.

---

## Appendix A — Frozen contract (hooks & schema)

**CSS hooks Coder places in `index.html` (Designer targets these):**

- `.dashboard` — root container.
- `.dashboard__header` — title/description block.
- `.dashboard__grid` — card grid container.
- `.project-card` — one per project.
- `.project-card__title`, `.project-card__owner`,
  `.project-card__activity`, `.project-card__meta`.
- `.badge`, `.badge--on-track`, `.badge--at-risk`, `.badge--blocked`,
  `.badge--done`, `.badge--unknown`.
- `.priority`, `.priority--low`, `.priority--medium`, `.priority--high`,
  `.priority--critical`, `.priority--unknown`.
- `.dashboard__empty`, `.dashboard__error` — empty/error states.

**JSON schema for `app/project-data.json`:**

```json
{
  "projects": [
    {
      "name": "string (required)",
      "owner": "string (required)",
      "status": "on-track | at-risk | blocked | done (required)",
      "recentActivity": "string (required)",
      "priority": "low | medium | high | critical (required)",
      "progress": 0,
      "dueDate": "YYYY-MM-DD",
      "summary": "string (optional, short)"
    }
  ]
}
```

Unknown `status` or `priority` values must not break rendering; the UI
falls back to `.badge--unknown` / `.priority--unknown`.
