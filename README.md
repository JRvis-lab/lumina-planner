# Lumina Planner

A personal daily planner PWA built on the Lumina Sanctorum design language — deep abyss blue, alchemic gold, biolume cyan, and parchment. Designed for one user, one phone, zero friction.

**Live at:** https://jrvis-lab.github.io/lumina-planner/

---

## What it is

Lumina Planner is a standalone, offline-capable web app that lives on your home screen. No accounts, no backend, no subscriptions. Everything is stored in `localStorage` — your data stays on your device.

The philosophy is **capture-first, structure-later**: ideas land in The Vault raw and unfiltered, then get promoted into the Week Plan or attached to Projects when you're ready to commit. A force-directed Tree view maps your project landscape visually. One project can be pinned as your **North Star** — your primary deliverable — and it appears as a persistent banner throughout the app.

---

## The four tabs

### ⬡ Vault
Zero-friction capture, typed or spoken. Items sit here until you're ready to act on them. Each item has a priority (Low / Med / High) that you can cycle before promoting.

**Promote flow:** tap the `→` button on any item to choose where it goes:
- **This Week / Next Week** → moves it into the Week Plan
- **New Project** → creates a project from the item text
- **Attach to Project** → links it as a child task under an existing project

Tag anything on capture using `#tag` syntax: `fix the nav bar #robotics` becomes the text "fix the nav bar" with a `robotics` tag.

### 📅 Plan
Weekly view with a **This Week / Next Week** toggle. Items can optionally be assigned to a specific day (tap the day pill on any task). Priority cycling, completion (check ring), and a collapsible done section with carry or clear.

### 🗂 Projects
Ongoing work with no single completion date. Each project can have child tasks (expand to add inline, Enter = next task, Esc = close). A progress bar tracks done vs. total children. Tap the star `★` to pin a project as your **North Star** — you'll be asked to confirm if one is already set. Promote any child task to the Week Plan in one tap.

Tag projects with `#tag` syntax on creation. Tags are colour-coded throughout the app.

### 🕸 Tree
A force-directed SVG graph of your project landscape. Nodes represent projects, edges represent links between them (set via `project.links`). Drag nodes to reposition — positions are saved. Tap a node to open that project in the Projects tab. The North Star node glows gold with a progress arc. Node colour is determined by domain tag.

---

## North Star

One project at a time can be your North Star — your primary active deliverable. Once set, a gold banner appears in the header across all tabs. Tap it to jump to the Projects tab. Change it at any time; you'll be asked to confirm the switch.

---

## Voice capture

Uses the Web Speech API. Available on the **Vault** and **Projects** tabs. Works in **Chrome** (recommended). Not supported in Firefox or Brave (Brave blocks the Google speech server by design).

Error messages are explicit: denied mic / no speech detected / network failure / unsupported browser.

---

## New Day Transition

Fires on first open of a new calendar day. Shows yesterday's stats (done / unfinished / in vault) and gives you the choice to **carry forward** unfinished tasks or **start fresh** and drop them.

---

## File structure

Everything needed to run the app lives in `index.html`. The other files are the PWA layer.

```
index.html      — Entire app (HTML + CSS + JS, single file, no build step)
manifest.json   — PWA manifest (name, icons, display mode, theme colour)
sw.js           — Service worker (cache: lumina-v10, offline support)
icon-192.png    — App icon 192×192 (Android home screen)
icon-512.png    — App icon 512×512 (splash screens)
```

---

## Deployment

Deployed via GitHub Pages from the `main` branch root. No build step, no dependencies, no Node. Push files and GitHub handles the rest.

**When updating any file:** always bump the cache name in `sw.js` (e.g. `lumina-v10` → `lumina-v11`) in the same commit. Without this, the service worker will keep serving the cached version to returning users.

**To update the installed PWA on your homescreen:** uninstall the app icon first, push your changes, then reinstall from Chrome.

---

## State schema

All state is stored under `localStorage` key `jr-lumina-v2`.

```js
S = {
  items:      [],       // status: 'vault' | 'planned' | 'done' | 'child'
  projects:   [],       // ongoing work nodes
  lastDate:   null,     // ISO date string — drives New Day check
  tab:        'vault',  // active tab
  showDone:   false,
  northStar:  null,     // project ID
  tags:       [],       // global tag list
  treeLayout: {},       // { [projectId]: {x, y} }
  planWeek:   'this',   // 'this' | 'next'
}
```

**Item fields:** `id, text, created, status, priority, tag, parentProject, week, day`  
**Project fields:** `id, text, created, priority, tag, children[], links[]`

Migration is additive — old data from prior schema versions is preserved on load.

---

## Design system

Lumina Planner uses the **Lumina Sanctorum** visual language.

| Token | Value | Role |
|---|---|---|
| Abyss | `#0A1931` | Primary background |
| Abyss Mid | `#112d55` | Surface / card background |
| Alchemic Gold | `#D4AF37` | Primary accent, North Star, headings |
| Biolume Cyan | `#7DF9FF` | Secondary accent, active states, glow |
| Parchment | `#F5F5DC` | Body text |
| Living Emerald | `#28A745` | Done / success states |
| Violet | `#C080E0` | Tertiary accent, project nodes |
| Danger | `#EF4444` | High priority, delete actions |

**Typography:** Playfair Display (headings) · Outfit (body) · JetBrains Mono (metadata, labels, tags)

**Glass cards:** `rgba(18,45,85,0.62)` + gold border + `backdrop-filter: blur(16px)`

---

## Part of the JRvis-lab ecosystem

Lumina Planner shares its design language with the Lumina Sanctorum Mind Palace project and the JRvis desktop AI companion. The visual system — colours, typography, glassmorphism panels, gem crystal motif — is intentionally consistent across all three.
