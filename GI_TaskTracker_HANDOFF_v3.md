# GI Task Tracker — Developer Handoff v3
*Continuation from previous session — for new Claude/chat handoff*

---

## Current State Summary

**Live URL:** https://kyleharmer.github.io/gi-tracker  
**GitHub repo:** kyleharmer/gi-tracker  
**Upload file as:** `index.html`

**Latest confirmed working version:** `v11.6` / `gi_task_tracker_v11_6_fixed.html`  
**Confirmed working reference:** `v10` / `gi_task_tracker_firebase10.html`  
**Broken/incomplete file to avoid as a base:** `gi_task_tracker_v11_5.html`

---

## ✅ Current Status — Major Sync Bug Resolved

### User verification
Kyle tested the v11.6 fixed build and confirmed:

- Tasks now save correctly.
- Tasks persist after refresh.
- Merge/update behavior appears to work locally.
- Cross-PC behavior appears to work on other PCs.
- The previous disappearing-task issue is resolved.

### Fixed version
Use this file as the current base going forward:

```text
gi_task_tracker_v11_6_fixed.html
```

This was built by starting from the known-working v10 file and surgically applying only the safer v11 sync/merge changes.

---

## Root Cause of the v11.5 Failure

The earlier handoff suspected a Firebase versioning/read-write issue. That was reasonable based on the v11.0–v11.3 symptoms, but the uploaded `v11_5` file revealed a larger issue.

### Actual v11.5 issue
`gi_task_tracker_v11_5.html` was missing most of the main application JavaScript.

It contained the Firebase/sync shell and some row interaction code, but did **not** include the full core app block from v10, including items such as:

- `state`
- `GLOBAL_COLUMNS`
- `GLOBAL_BUCKETS`
- `TAB_BUCKETS`
- `uid()`
- `esc()`
- `initDefault()`
- `renderApp()`
- modal/edit functions
- reporting/dashboard/config functions
- other main UI logic

Because of this, `initApp()` could load from Firebase but then fail when it called functions or referenced variables that did not exist.

### Likely runtime failures in v11.5
Examples:

```javascript
ReferenceError: state is not defined
```

when `applyRemoteState(remote)` tried to write to `state.tabs`, and then additional failures such as:

```javascript
ReferenceError: initDefault is not defined
ReferenceError: renderApp is not defined
```

### Key conclusion
Do **not** continue from `v11_5`. It is incomplete. Future work should use `v11.6` as the base.

---

## v11.6 Fix Summary

### Strategy used
Instead of trying to repair the incomplete v11.5 shell, v11.6 was rebuilt from the confirmed-working v10 HTML file.

Then the intended v11 sync improvements were reapplied surgically.

### Functions/areas changed
- `APP_VERSION`
- Firebase REST unwrap/wrap behavior where needed
- SSE remote-state unwrap behavior
- `mergeStates()` added
- `acceptUpdate()` updated to merge instead of overwrite
- task rendering updated to show a `⚠` conflict badge
- `saveTask()` updated to clear conflict flags when a task is saved

### Preserved from v10
- Full main application JavaScript
- Existing app state structure
- Existing UI/modal/reporting/config code
- Existing Firebase REST URL behavior, including `?auth=`
- Existing v10-compatible version bump behavior
- Existing Firebase data format

### Firebase data format — DO NOT CHANGE
```javascript
// Write format:
{ d: JSON.stringify(state), v: versionNumber, ts: isoString, savedBy: userName }

// Read/unwrap:
if (raw.d && typeof raw.d === 'string') inner = JSON.parse(raw.d);
```

---

## Firebase Credentials

```text
Project ID:  gi-task-tracker
Database URL: https://gi-task-tracker-default-rtdb.firebaseio.com
API Key:      AIzaSyAHLU7QSQmaONIUAAVOt8mgzyYsLaMxFTw
Data path:    tasktracker/state
Stream path:  tasktracker/state
Rules:        { ".read": true, ".write": true }
```

---

## Version History of Recent Fixes

### v11.6 — Confirmed Working Sync/Merge Recovery
- Rebuilt from v10 because v11.5 was missing the main app JS.
- Preserved working v10 Firebase behavior.
- Reapplied Firebase `{ d: JSON.stringify(state) }` wrap/unwrap handling.
- Reapplied SSE unwrap handling.
- Added/restored `mergeStates()`.
- Updated `acceptUpdate()` to merge remote changes instead of blindly overwriting local state.
- Added visible `⚠` conflict badge for merged/conflicted tasks.
- Updated `saveTask()` to clear conflict flags after review/save.
- User confirmed saves, refresh persistence, local merge behavior, and other-PC sync appear to work.

### v11.5 — Broken / Do Not Use as Base
- File was incomplete.
- Firebase shell existed, but most main app functions and state definitions were missing.
- App could appear partially loaded but would fail during initialization/rendering.

### v11.3 — Superseded Diagnosis
- Earlier attempt focused on REST auth removal and version-bump behavior.
- Later comparison showed v10’s `?auth=` behavior works for this Firebase project.
- Do not use v11.3 as the base.

### v11.2 — Real-time Merge Concept
- Introduced the intended field-level merge idea.
- This concept was carried forward into v11.6.

### v11.1 — Firebase Connection Concept
- Introduced Firebase wrap/unwrap fixes.
- This concept was carried forward into v11.6.

### v10.0 — Last Stable Pre-Rebuild
- Confirmed working Firebase/save/sync baseline.
- Used as the base for v11.6.

---

## Versioning Rules

- Bug fixes → point release, for example `v11.6` → `v11.7`
- New features → major/minor version depending on size, for example `v11.6` → `v12.0`
- Always bump the `APP_VERSION` constant.
- Always add a changelog entry.

---

## Surgical Edit Rules

Before any future code change, state which functions will be touched and why.

After any future code change, state what was **not** changed.

Do not rebuild unrelated sections.

Never change the Firebase `{ d: JSON.stringify(state) }` wrap format unless the database is intentionally migrated.

Do not use `v11_5` as the base file.

---

## What Still Needs To Be Done

### Priority 1 — Continue testing v11.6
- Continue normal usage on multiple PCs.
- Watch for any duplicate tasks, missed merges, or stale update banners.
- If a sync bug appears, debug from `v11.6`, not from `v11_5` or `v11.3`.

### Priority 2 — Pending features

1. **Reorder Buckets and Columns in Configure panel**
   - Desktop: drag handle (`⋮⋮`) on each row
   - Mobile: Up/Down arrow buttons
   - Order persists to `state.tabBuckets[tab]` and `state.tabCols[tab]`

2. **Rename terminology**
   - Possible rename: `Buckets` → `Sections`
   - Possible rename: `Columns` → `Fields`
   - Pending Kyle's approval before implementation

---

## People / Context

- **Kyle Harmer** — Ammunition PM at General Inspection LLC; built/owns this tool
- **Chris Alexander** — Kyle's manager
- **Favs / Michael Del Favero** — PM whose Excel frustration helped spark the project
- **Other PMs/users:** Noah McLaughlin, Kendra Fair, Jason McCowan, Service-Jonah, Design Bob & Cody
- **Management viewers:** Haliey Holmes + several others
