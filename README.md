Lumina Planner
A personal daily planner PWA built on the Lumina Sanctorum design language — deep abyss blue, alchemic gold, biolume cyan, and parchment. Designed for one user, one phone, zero friction.
Live at: https://jrvis-lab.github.io/lumina-planner/
What it is
Lumina Planner is a standalone, offline-capable web app that lives on your home screen. No accounts, no backend, no subscriptions. Everything is stored in localStorage — your data stays on your device.
The philosophy behind the structure is capture-first, structure-later: ideas land in The Vault raw and unfiltered, then get promoted into the Day Plan when you're ready to commit them to a time block. Projects sit separately as ongoing context. Focus mode locks you into one task at a time with a Pomodoro timer.
Features
The Vault captures anything — typed or spoken via the microphone button. Items sit here until you promote them to the Day Plan with a time block assignment (Morning, Afternoon, or Evening). Priority (Low / Med / High) can be set before promoting.
Day Plan organises your promoted tasks into three time blocks. Tap the check ring to mark done. Progress bar in the header tracks completion across the day. Completed tasks fold into a collapsible section at the bottom.
Projects holds ongoing work with no single completion date — think active freelance gigs, long-running builds, research threads. Also supports voice capture.
Focus Mode pulls all planned tasks into a priority-sorted queue and pairs them with a Pomodoro timer (5 / 15 / 25 minute presets). Includes motivational nudges, skip, and a "Break down" button that sends the current task back to The Vault pre-labelled for subtask capture.
Sticky Notes panel (📌, top-left under the header) stores freeform notes that persist across sessions. Four colour variants, always accessible from any tab.
New Day Transition fires on first open of a new calendar day. Shows yesterday's stats (done / unfinished / in vault) and gives you the choice to carry forward unfinished tasks or start fresh.
Voice Capture uses the Web Speech API. Available on The Vault and Projects tabs. Works in Chrome and Brave; not supported in Firefox.
Offline Support via a service worker that caches the app shell and assets on first load. The app works fully offline after that.
File structure
The repo contains five files. Everything needed to run the app is self-contained in index.html — the other files are the PWA layer on top.
index.html      — The entire app (HTML + CSS + JS, single file)
manifest.json   — PWA manifest (name, icons, display mode, theme colour)
sw.js           — Service worker (cache strategy, offline support)
icon-192.png    — App icon at 192×192 (used by Android)
icon-512.png    — App icon at 512×512 (used for splash screens)
Deployment
The app is deployed via GitHub Pages from the main branch root. No build step, no dependencies, no Node. Just push files and GitHub handles the rest.
When updating any file, always upload an updated sw.js with a bumped cache version (e.g. lumina-v3 → lumina-v4). Without this, the service worker will serve the old cached version to returning users and changes won't appear.
Design system
Lumina Planner uses the Lumina Sanctorum visual language developed across the JRvis-lab project family.
Token
Value
Role
Abyss
#0A1931
Primary background
Abyss Mid
#112d55
Surface / card background
Alchemic Gold
#D4AF37
Primary accent, headings, CTA
Biolume Cyan
#7DF9FF
Secondary accent, active states, glow
Parchment
#F5F5DC
Body text
Living Emerald
#28A745
Done / success states
Violet
#C080E0
Tertiary accent (Focus break button)
Danger
#EF4444
High priority, delete actions
Typography stack: Playfair Display (headings) · Outfit (body) · JetBrains Mono (metadata, labels, timers).
Keyboard shortcuts (desktop / desktop Brave)
Ctrl+K / ⌘+K jumps to The Vault and focuses the input. Enter saves the current input. Escape closes any open panel or promo picker. Ctrl+Enter / ⌘+Enter saves inside sticky notes or the active input.
Part of the JRvis-lab ecosystem
Lumina Planner shares its design language with the Lumina Sanctorum Mind Palace project and the JRvis desktop AI companion. The visual system — colours, typography, glassmorphism panels — is intentionally consistent across all three.
