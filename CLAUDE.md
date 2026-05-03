# CLAUDE.md — lumina-planner

Project-level config for Claude Code sessions on the `JRvis-lab/lumina-planner` repo.

---

## What this repo is

A single-file PWA daily planner. No framework, no build step, no dependencies.
Five files total — everything runs from `index.html`.

```
index.html      — entire app (HTML + CSS + JS)
sw.js           — service worker
manifest.json   — PWA manifest
icon-192.png    — home screen icon
icon-512.png    — splash icon
```

---

## THE ONE RULE

**Every file change = bump `sw.js` cache version in the same commit.**
Current version: `lumina-v11`
Pattern: `const CACHE = 'lumina-v11';` → increment to `v12`, `v13`, etc.
Failure to do this means users get stale cached builds. No exceptions.

---

## Architecture

Single HTML file. State in `localStorage` under key `jr-lumina-v2`.
No modules, no imports, no bundler. Everything inlined.

### State object `S`

```js
S = {
  items:      [],       // {id, text, created, status, priority, tag, parentProject, week, day}
  projects:   [],       // {id, text, created, priority, tag, children:[], links:[]}
  lastDate:   null,     // ISO date string — drives New Day modal
  tab:        'vault',  // active tab — persisted
  showDone:   false,
  northStar:  null,     // project ID pinned as primary deliverable
  tags:       [],       // global tag registry
  treeLayout: {},       // {[projectId]: {x, y}} — saved node positions for Tree tab
  planWeek:   'this',   // 'this' | 'next'
}
```

**Item statuses:** `'vault'` | `'planned'` | `'done'` | `'child'`

**Runtime-only (not persisted — reset on load):**
`_exp[]`, `_dayPick`, `_childInput`, `_pendingNS`, `_treeNodes`, `_treeDrag`, `_rAFPending`

### Migration rule
Schema changes must be **additive only**. Old items survive. New fields get defaults in `load()`. Never break existing `jr-lumina-v2` data.

---

## The four tabs

| Tab | ID | Panel | Input bar |
|---|---|---|---|
| Vault | `tab-vault` | `panel-vault` | ✓ |
| Plan | `tab-plan` | `panel-plan` | ✓ |
| Projects | `tab-projects` | `panel-projects` | ✓ |
| Tree | `tab-tree` | `panel-tree` | ✗ (hidden) |

Tab switching is done via `switchTab(name)` — this is the only function that updates active CSS classes AND calls `renderAll()`. Never call `renderAll()` alone for a tab switch.

---

## Design system (Lumina Sanctorum) — NON-NEGOTIABLE

All visual changes must use these CSS tokens. Never hardcode hex values directly.

| Token | CSS var | Hex |
|---|---|---|
| Abyss | `--abyss` | `#0A1931` |
| Abyss Mid | `--abyss-mid` | `#112d55` |
| Gold | `--gold` | `#D4AF37` |
| Cyan | `--cyan` | `#7DF9FF` |
| Parchment | `--parchment` | `#F5F5DC` |
| Emerald | `--emerald` | `#28A745` |
| Violet | `--violet` | `#C080E0` |
| Danger | `--danger` | `#EF4444` |

**Fonts:** Playfair Display (headings), Outfit (body), JetBrains Mono (metadata/labels/tags)
**Glass:** `rgba(18,45,85,0.62)` + gold border + `backdrop-filter:blur(16px)`

---

## Key UX patterns

### Input bar button (single slot)
The `#add-btn` is dual-purpose. `syncBtn()` controls what it shows:
- **Input empty + mic-capable tab (vault/projects):** shows mic icon, `class="add-btn off"`, triggers `toggleMic()`
- **Input has text:** shows `+` icon, `class="add-btn rdy"`, triggers `handleAdd()`
- **Listening:** shows mic icon, `class="add-btn listening"`, triggers `_rec.stop()`

Always call `syncBtn()` after any state change that might affect input content.

### Day assignment smart logic
`setDay(id, day, isPast)` — if `isPast=true`, auto-bumps `week` to `'next'`.
Past detection: `day-of-week < today's DOW`, or if today is Sunday (DOW=0), all Mon-Sat are past.
Past days render with `.day-past` class (strikethrough + reduced opacity).

### Voice (Web Speech API)
Works in Chrome only. Brave blocks the Google speech server. Not supported in Firefox.
Error codes mapped to human messages: `not-allowed`, `no-speech`, `network`, catch-all.
Mic state lives in `_recOn` boolean. `_rec` is null if SR unsupported.

### Gem crystal animation
Inline SVG hexagon with `@keyframes gemSpin` — 5s linear, rotates 360°, filter cycles cyan→gold→violet glow. **No sprite sheets.** Do not replace with external images.

### North Star
`S.northStar` = project ID or null. Banner shows in header when set. Changing it triggers confirm modal if one is already set. Deleting a project clears `northStar` if it was that project.

### Tree tab
SVG force-directed graph. Node positions saved to `S.treeLayout`. Tap = open in Projects. Drag = reposition. rAF-throttled re-render on drag. Force sim only runs on first render (when layout is empty).

---

## What's NOT in scope (do not add without explicit confirmation)

- Recurring tasks
- Pomodoro / focus timer
- Sticky notes
- Multi-user / cloud sync
- Notifications (under consideration, not confirmed)
- Framework migration (React, Vue, etc.)
- File splitting / build step
- Keyboard shortcuts

---

## Working style for this repo

- Make targeted edits. Don't rewrite sections that aren't touched by the task.
- After any JS function change, check for all callers — function signatures changed here have historically caused silent bugs.
- If a change affects the schema, update `load()` migration AND `DEF` object AND this CLAUDE.md.
- Test mental model: single user, Chrome Android, offline-capable, home screen PWA.
