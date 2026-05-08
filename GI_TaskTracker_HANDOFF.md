# GI Task Tracker — Developer Handoff Document
*Created to continue development in a new Claude chat session*

---

## Project Overview
A single-file HTML web app replacing a shared Excel task tracker for General Inspection LLC. 8 PM tabs, real-time shared data via Firebase, hosted on GitHub Pages. Built entirely through vibe coding with Claude over one session.

**Live URL:** https://kyleharmer.github.io/gi-tracker  
**GitHub repo:** kyleharmer/gi-tracker  
**Upload file as:** `index.html`

---

## Firebase Credentials
```
Project ID:  gi-task-tracker
Database URL: https://gi-task-tracker-default-rtdb.firebaseio.com
API Key:      AIzaSyAHLU7QSQmaONIUAAVOt8mgzyYsLaMxFTw
Data path:    tasktracker/state
Rules:        { ".read": true, ".write": true }  ← must be published
```

**Data format:** State is wrapped as `{ d: JSON.stringify(state), v, ts, savedBy }` before writing to Firebase. This avoids key character restrictions (`/ . # $ [ ]`) in Firebase. The `fbRead()` function unwraps it. **Do not change this format or all existing data will break.**

---

## ⚠️ KNOWN ISSUE — Fix This First (v11)

**Problem:** v11 has a Firebase connection issue. It shows "Check Firebase rules" even though rules are correct. v10 works fine with the same rules.

**What's known:**
- The SSE stream 401 error was fixed (removed `auth=` param from stream URL) — that part is resolved
- But the app is still showing offline/error on load in v11
- v10 (`gi_task_tracker_firebase.html` from the previous working session) connects and shows "Synced" correctly
- The difference between v10 and v11 is the full rebuild from source JS files — something in that rebuild broke the Firebase read/write flow

**Likely suspects to investigate:**
1. The `fbRead()` unwrapping logic — check that it correctly handles the `{d: "..."}` wrapped format
2. The `fbWrite()` wrapping — verify it still wraps as `{d: JSON.stringify(data), v, ts, savedBy}`
3. The `initApp()` flow — in the rebuild, verify it calls `fbRead()` then `fbWrite()` on first empty load
4. Compare the REST layer in v11 against the known-working v10 file to spot differences

**Working reference:** The v10 file is at `/mnt/user-data/outputs/gi_task_tracker_shared.html` — actually check git history or use the file the user has locally as "v10" since that's confirmed working.

---

## File Locations (Claude container /tmp/)

These JS source files exist in `/tmp/` and are the building blocks:

| File | Contents |
|------|----------|
| `/tmp/gi_firebase_rest.js` | Firebase REST layer (fbRead, fbWrite, SSE stream, persist, initApp) |
| `/tmp/gi_main_v4c.js` | Main app JS (renderApp, modals, dashboard, report, config) |
| `/tmp/gi_v11_patched.js` | v11 fully patched JS (REST + main combined, all features) |
| `/tmp/gi_v11_base.js` | v11 base before patches |
| `/tmp/seed_data.js` | All 173 tasks across 8 tabs (seedAll function body only) |
| `/tmp/logo_b64.txt` | v1 logo — original small JPEG banner (do not use for header) |
| `/tmp/new_logo_b64.txt` | v2 logo — GII-LOGO-TRANSPARENT copy, JPEG, USE THIS for header/report |
| `/tmp/new_app_logo.txt` | v3 logo — AI-generated app icon, USE THIS for PWA/favicon/icons only |
| `/tmp/icon2_180.txt` | PWA icon 180×180 from v3 logo (apple-touch-icon) |
| `/tmp/icon2_192.txt` | PWA icon 192×192 from v3 logo (manifest + favicon) |
| `/tmp/icon2_512.txt` | PWA icon 512×512 from v3 logo (manifest) |
| `/tmp/build_v3.py` | Contains the full CSS as a Python raw string — extract with regex |

**Critical:** The seed data must be injected into the JS via `.replace('SEED_DATA_PLACEHOLDER', seed_data)`. In v11 this was accidentally skipped causing empty tabs on load.

**CSS extraction pattern:**
```python
import re
with open('/tmp/build_v3.py') as f:
    build = f.read()
m = re.search(r'css = r"""(.*?)"""', build, re.DOTALL)
css = m.group(1)
```

---

## Logo Usage Rules
| Location | Logo | Format | Blend Mode |
|----------|-------|--------|------------|
| App header (`<img class="hdr-logo">`) | v2 (`new_logo_b64.txt`) | JPEG | `mix-blend-mode:screen; opacity:0.85` |
| PDF report header | v2 (`new_logo_b64.txt`) | JPEG | `mix-blend-mode:screen; opacity:0.85` |
| Browser tab favicon | v3 (`new_app_logo.txt` / `icon2_192.txt`) | PNG | None needed |
| iOS home screen | v3 (`icon2_180.txt`) | PNG | None needed |
| Android/PWA | v3 (`icon2_192.txt`, `icon2_512.txt`) | PNG | None needed |

**Why blend mode on v2:** The PNG file has a black background baked in (not truly transparent). `mix-blend-mode: screen` makes black pixels invisible on the dark green header. The v3 logo has a clean white/rounded background so it works as-is for icons.

---

## Architecture

```
Single HTML file
├── <head>
│   ├── PWA manifest (inline data URI)
│   ├── Favicon + apple-touch-icon links
│   ├── Chart.js CDN
│   ├── Google Fonts (async, non-blocking)
│   └── All CSS (embedded)
├── <body>
│   ├── Header bar (logo, title, sync dot, user chip, buttons)
│   ├── Update banner (shown when another user saves)
│   ├── Tab bar
│   ├── Main content area (#appContent)
│   ├── Save indicator (fixed, top-right)
│   ├── 6 modals (task, tab, config, history, report, user name)
│   ├── Mobile action sheet
│   └── <script> — all JS embedded
└── JS structure:
    ├── Firebase REST layer (fbRead, fbWrite, SSE stream)
    ├── User identity (name prompt, localStorage)
    ├── State management (serialize, apply, cache)
    ├── Persist (debounced 800ms, wraps state as {d:"..."})
    ├── Main app (renderApp, renderTabs, renderDashboard, etc.)
    ├── Seed data (seedAll — 173 tasks, runs on empty DB)
    └── Boot: initApp() called at bottom
```

---

## Current Feature Set (v11)

- **8 PM tabs:** Kyle, Favs, Noah, Kendra, Software-Kendra, Service-Jonah, Jason, Design Bob & Cody
- **Per-tab buckets** (custom groupings within each tab, e.g. "Active Projects", "Shipped / HIT List")
- **Per-tab columns** (configurable visible fields per tab)
- **Task fields:** Customer, Details/Notes, Owner (green chip), Supporting (blue chip), Priority, Status, Due Date, Next Milestone, Meeting Notes
- **Autocomplete** on Owner/Supporting — folksonomy, learns from entered data
- **Due date color coding** + countdown badges (≤14 days shows "3 days left" etc.)
- **Completed tasks** hidden by default, toggle to show with count, greyed/struck-through
- **Sorting** by any column header
- **Search + filter** by status and priority per tab
- **Dashboard** — 4 stat cards, 3 charts (bar/donut/stacked), filterable master table
- **My Tasks view** — search any name, see all tasks across all tabs
- **Change log per task** — records what changed, when, who (username + timestamp)
- **Tab change history** — add/remove/rename logged, visible in Dashboard
- **Report generation** — PDF via browser print, configurable scope/filters/sections, includes charts and logo
- **Export CSV** per tab and all tabs
- **Real-time sync** — Firebase SSE push, update banner when others save
- **Backup/Restore** — JSON download/upload in header
- **User identity** — name prompt on first open per device, stored in localStorage, shown in header chip, clickable to change
- **Mobile:** tap row → action sheet (Edit/History/Delete). Fixed bug where Edit/History did nothing — values now captured before closeActionSheet() nulls them
- **Desktop:** double-click row → edit. Hover shows action buttons + "double-click to edit" hint
- **PWA:** installable on iOS/Android/desktop, GI icon on home screen
- **Font smoothing** — Segoe UI fallbacks for Windows, antialiased globally
- **Version badge** in header (v11.0), `APP_VERSION` constant at top of JS

---

## Pending Features (not yet built)

### 1. Reorder Buckets and Columns in Configure panel
**What:** Currently buckets (task groupings) and columns (fields) are in fixed order within the Configure panel. Users want to drag-and-drop or use up/down arrows to reorder them.

**Approach discussed:**
- Desktop: drag handle (⋮⋮) on each row in the Configure panel, drag to reorder
- Mobile: Up/Down arrow buttons (drag-and-drop in a modal is unreliable on touch)
- Order persists to `state.tabBuckets[tab]` (already an array, just needs reordering) and `state.tabCols[tab]` (same)

### 2. Rename "Buckets" and "Columns" in UI
**What:** The terms "Buckets" and "Columns" are used throughout the Configure panel. Kyle wants more business-forward terminology but hasn't settled on specific words yet. Current candidates discussed: "Sections" (for buckets) and "Fields" (for columns) — but Kyle wasn't fully sold. Needs to be decided before renaming.

---

## Key Implementation Details

### State structure
```javascript
state = {
  tabs: ['Kyle', 'Favs', ...],
  activeTab: 'Kyle',
  tasks: {
    'Kyle': {
      'Active Projects': [{ id, customer, notes, owner, support, 
                            status, due, priority, milestone, 
                            meetingNotes, changelog, completedAt }],
      'Shipped / HIT List': [...]
    }
  },
  timestamps: { 'Kyle': '05/06/2026, 09:53 AM (Kyle)' },
  tabCols: { 'Kyle': [{ key, label, type, visible }] },
  tabBuckets: { 'Kyle': ['Active Projects', 'Shipped / HIT List', ...] },
  tabHistory: [{ t, action, tab, by }]
}
```

### Firebase write format
```javascript
// ALWAYS wrap before writing:
{ d: JSON.stringify(state), v: localVersion+1, ts: isoString, savedBy: currentUser }

// ALWAYS unwrap after reading:
if (raw.d && typeof raw.d === 'string') inner = JSON.parse(raw.d);
```

### Persist flow
1. `persist()` — debounces 800ms, caches to localStorage immediately
2. `persistNow()` — calls `fbWrite(serializeState())`, sets `ignoreNextStream=true` to suppress own echo
3. On success: updates `localVersion`, shows "Saved by Kyle" indicator
4. On failure: falls back gracefully, shows "Saved locally only"

### SSE stream
- URL: `FB_DB_URL + '/' + STREAM_NODE + '.json'` — **NO auth param** (causes 401 even with open rules)
- Listens for `put` events, compares `remote.v > localVersion` before showing update banner
- Reconnects automatically on error with 10s delay

### Syntax gotchas to watch for
- `</script>` inside JS string literals breaks HTML parser — always split as `'</scr'+'ipt>'`
- Seed data uses `.push(mk(...))` — `mk()` must be defined before `seedAll()` runs
- `SEED_DATA_PLACEHOLDER` in JS template must be replaced before writing HTML file
- Firebase key restrictions: never store objects with keys containing `. # $ [ ] /` — use the `{d:"..."}` wrap

---

## Build Pattern (how to assemble the HTML)

```python
# 1. Load parts
with open('/tmp/gi_firebase_rest.js') as f: rest_layer = f.read()
with open('/tmp/gi_main_v4c.js') as f: main_js = f.read()
with open('/tmp/seed_data.js') as f: seed_data = f.read()

# 2. Combine JS
full_js = rest_layer + "\n\n// ═══ MAIN APPLICATION ═══\n\n" + main_js

# 3. Inject seed data
full_js = full_js.replace('SEED_DATA_PLACEHOLDER', seed_data)

# 4. Apply patches to full_js as needed

# 5. Verify syntax
# node --check with a browser API stub

# 6. Assemble HTML using string concatenation (not f-strings — logo b64 causes unicode issues)
parts = ['<!DOCTYPE html>...', css, '...', logo_b64, '...', full_js, '...']
html = ''.join(parts)

# 7. Fix script tag split
html = html.replace("chart.umd.js\"></script>';\n  html+='",
                    "chart.umd.js\"></scr'+'ipt>';\n  html+='")

# 8. Write output
with open('/mnt/user-data/outputs/gi_task_tracker_firebase.html', 'w') as f:
    f.write(html)
```

---

## Release Checklist
Every release must include both files:
- [ ] `gi_task_tracker_firebase.html` → rename to `index.html` → upload to GitHub
- [ ] `GI_TaskTracker_CHANGELOG.md` → upload to GitHub alongside index.html
- [ ] Bump `APP_VERSION` constant at top of JS
- [ ] Add entry to top of changelog
- [ ] Hard refresh after GitHub Pages rebuild (Ctrl+Shift+R)

---

## People / Context
- **Kyle Harmer** — Ammunition PM at General Inspection LLC, built this tool
- **Chris Alexander** — Kyle's manager, introduced the shared Excel task list
- **Favs (Michael Del Favero)** — PM, whose frustration with Excel sparked this project
- **Other PMs:** Noah McLaughlin, Kendra Fair, Jason McCowan + Service-Jonah, Design Bob & Cody
- **Management viewers:** Haliey Holmes + ~3 others (read-only use case)
- **Long-term hosting:** geninsp.com (when available) — data migrates seamlessly, just change the HTML host
