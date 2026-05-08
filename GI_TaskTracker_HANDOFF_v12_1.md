# GI Task Tracker — Developer Handoff v7
*Continuation from previous session — includes confirmed v11.6 recovery, surgical future-development workflow, confirmed production/staging GitHub/Firebase split, and release-promotion rules*

---

## Current State Summary

**Production/live URL:** https://kyleharmer.github.io/gi-tracker  
**Production GitHub repo:** `kyleharmer/gi-tracker`  
**Testing/staging URL:** https://kyleharmer.github.io/gi-tracker-testing/  
**Testing/staging GitHub repo:** `kyleharmer/gi-tracker-testing`  
**Upload file as:** `index.html` in whichever repo/folder Kyle is actively managing

**Latest generated staging build:** `v12.1` / updated `index.html` — hidden admin-only Backup / Restore controls
**Latest confirmed working base before this change:** `v12.0` workflow-ready `index.html` / v11.6-or-newer confirmed app base  
**Confirmed working reference:** `v10` / `gi_task_tracker_firebase10.html`  
**Broken/incomplete file to avoid as a base:** `gi_task_tracker_v11_5.html`

**Production Firebase node/path:** `tasktracker/state`  
**Testing/staging Firebase node/path:** `tasktracker_staging/state`  
**Important code naming:** The app currently uses `DATA_NODE` and `STREAM_NODE`, not `FB_PATH`. Both must point to the same environment.

**Confirmed setup note:** Kyle created the `tasktracker_staging` Firebase branch/path, split GitHub production/testing successfully, confirmed the live site still loads/connects, and confirmed the testing page is located at `https://kyleharmer.github.io/gi-tracker-testing/`.


## v12.1 Staging Change — Hidden Admin-Only Backup / Restore Controls

### Purpose
Backup and Restore are high-risk actions, especially Restore because it can overwrite the shared task tracker data for the entire team. In v12.1 staging, these controls are no longer visible as normal header buttons.

### Current behavior
- Backup and Restore live inside a hidden `#admin-tools` container.
- Normal users should not see either button.
- The functions `downloadBackup()` and `openRestoreDialog()` now call `requireAdminTools()` before running.
- Restore now has two confirmation prompts before the file picker opens.

### Hidden unlock method
1. Set the displayed user name to `Kyle` or `Kyle Harmer`.
2. Click the small version/environment label five times.
3. Enter the admin unlock code.
4. Backup and Restore appear for the current page session only.

### Important caution
This is an accidental-use prevention layer, not true authentication. The app is a static client-side HTML file, so anyone with source access could read or modify the unlock behavior. For the current goal, it prevents casual or accidental use by normal users once live.

### Files/functions touched
- Header Backup / Restore buttons moved into hidden `#admin-tools`.
- CSS added for `.admin-tools` and `.admin-tools.unlocked`.
- `APP_VERSION` bumped to `v12.1 — STAGING — tasktracker_staging/state`.
- Added admin helper functions: `isAdminUser()`, `applyAdminToolVisibility()`, `handleAdminUnlockClick()`, and `requireAdminTools()`.
- Updated `updateUserChip()` to bind the hidden unlock click handler to the version label.
- Guarded `downloadBackup()` and `openRestoreDialog()`.

### Firebase / data impact
No Firebase path, project, wrapped data format, task schema, sync, or merge logic changed. This is a UI/access-safety change only.


---

## Confirmed Production vs Testing Workflow

Kyle does **not** plan to maintain separate permanently named HTML files such as `index.production.html` and `index.staging.html` inside AI sessions. Kyle will organize files/folders manually outside the chat. Future AI sessions should return a normal `index.html` for the target environment requested.

### Default rule for all future development
Unless Kyle explicitly says the task is ready for live release, assume all coding work is being done for **testing/staging**.

Default future development target:

```javascript
var APP_VERSION = 'vXX.X STAGING';
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

Testing page:

```text
https://kyleharmer.github.io/gi-tracker-testing/
```

### Production release target
Only after Kyle confirms the staging/testing version works should a production/live release copy be prepared.

Production target:

```javascript
var APP_VERSION = 'vXX.X LIVE';
var DATA_NODE   = 'tasktracker/state';
var STREAM_NODE = 'tasktracker/state';
```

Production page:

```text
https://kyleharmer.github.io/gi-tracker/
```

### Critical rule: DATA_NODE and STREAM_NODE must match
The app uses two node variables:

- `DATA_NODE` controls normal Firebase read/write saves.
- `STREAM_NODE` controls the real-time update listener / “Load updates” behavior.

These must always point to the same environment:

Correct staging:

```javascript
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

Correct production:

```javascript
var DATA_NODE   = 'tasktracker/state';
var STREAM_NODE = 'tasktracker/state';
```

Never leave one on staging and the other on production.

### Visible label requirement
The visible version/environment label near the top of the app must make the active environment obvious.

Examples:

```text
v12.0 — STAGING — tasktracker_staging/state
v12.0 — LIVE — tasktracker/state
```

If the UI only displays `APP_VERSION`, then `APP_VERSION` should include enough wording to clearly identify the environment.

### Promotion checklist from staging to production
When a staged change is confirmed working:

1. Keep the tested code unchanged except for environment/version-label changes needed for live release.
2. Change `APP_VERSION` from `STAGING`/testing wording to `LIVE`/production wording.
3. Change both `DATA_NODE` and `STREAM_NODE` from `tasktracker_staging/state` to `tasktracker/state`.
4. Do not change Firebase project URL/API key, data wrapper format, merge logic, or unrelated UI during promotion.
5. Update the changelog with the final release notes.
6. Update this handoff with any workflow/architecture changes.
7. Upload the production `index.html` to the live/prod location only after the staging behavior is confirmed.


---

## Recommended Future Workflow — Fresh Chat / One Surgical Change

Use this handoff as the operating procedure for future AI-assisted development, especially when moving between Claude, ChatGPT, Cursor, or any other code assistant.

### Start each new work session with fresh files
For each bug fix or improvement, start a new chat/session and upload the current versions of:

1. `index.html` — the latest confirmed working app file
2. `GI_TaskTracker_CHANGELOG.md` — current changelog
3. `GI_TaskTracker_HANDOFF.md` — current handoff

The uploaded `index.html` is the source of truth for the app code. The changelog and handoff explain the history, known risks, and rules for safe editing.

### Work on one thing only
Each session should have one focused goal.

Good examples:

```text
Using the uploaded current working index.html, add reorderable Sections/Fields in the Configure panel. Make the smallest surgical changes possible, bump the version, and update the changelog/handoff.
```

```text
Using the uploaded current working index.html, fix the bug where the Load Updates banner appears but does not clear after merge. Do not change unrelated features. Bump the version and update changelog/handoff.
```

Avoid broad requests such as:

```text
Make the app better.
```

```text
Clean up the whole codebase and add the next few features.
```

Broad requests increase the risk of accidental rewrites, missing functions, Firebase regressions, or UI behavior changes.

### End each session with three updated artifacts
Each development session should produce:

1. Updated `index.html`
2. Updated changelog
3. Updated handoff

After confirming the app works, use those three updated files as the starting point for the next session.

### Treat each chat like a mini work order
Use this pattern:

```text
Input: current files + one goal
Work: inspect relevant functions, make smallest safe changes, syntax-check if possible
Output: updated app file + updated changelog + updated handoff
Stop: do not continue adding unrelated features in the same chat
```

This keeps the AI context clean and avoids the “chat got full” problem that caused confusion during the v11.x work.

---

## Required Prompt Template for Future AI Sessions

When starting a new session, use a prompt like this:

```text
I am uploading the current working GI Task Tracker index.html, changelog, and handoff file.

Please use the uploaded index.html as the only code base/source of truth. Do not rebuild from memory. Do not use any older broken versions as a base.

Goal for this session:
[Describe one bug fix or one feature only.]

Rules:
- Make the smallest surgical change possible.
- Before editing, identify the exact functions/sections you expect to touch and why.
- Preserve Firebase format: { d: JSON.stringify(state), v, ts, savedBy }.
- Do not change unrelated UI, state structure, Firebase paths, or versioning behavior.
- Do not refactor broadly.
- Bump `APP_VERSION` appropriately.
- Default to a staging/testing build unless I explicitly say this is a production/live release.
- For staging/testing work, use `DATA_NODE = 'tasktracker_staging/state'` and `STREAM_NODE = 'tasktracker_staging/state'`.
- For production/live release only, use `DATA_NODE = 'tasktracker/state'` and `STREAM_NODE = 'tasktracker/state'`.
- `DATA_NODE` and `STREAM_NODE` must always match the same environment.
- Clearly state which Firebase node/path the generated `index.html` uses.
- The visible version label/header at the top of the app must show which Firebase path/environment is connected, such as `v12.0 — STAGING — tasktracker_staging/state` or `v12.0 — LIVE — tasktracker/state`.
- Do not create separate named production/staging HTML files unless I explicitly ask; return the updated `index.html` for the requested environment and let me organize files/folders.
- Update the changelog and handoff.
- After editing, summarize what changed and what was intentionally not changed.
```

---

## AI Editing Guardrails — Very Important

### Do not rebuild the app from partial snippets
The v11.5 failure happened because a generated/rebuilt HTML file was missing most of the main application JavaScript. The file looked partly valid because the Firebase shell existed, but the real app functions were absent.

Future AI work must avoid rebuilding the app from memory, summaries, or partial source fragments unless Kyle explicitly approves a full rewrite.

### Do not continue from known-broken versions
Do not use any of these as the base:

- `gi_task_tracker_v11_5.html`
- `gi_task_tracker_v11_3.html`
- any v11.0–v11.5 file unless being used only for comparison

Use the latest confirmed working `index.html` / v11.6-or-newer file as the base.

### Preserve these core behaviors unless the requested change directly requires touching them
Avoid changing these areas unless the bug/feature specifically requires it:

- Firebase database URL/API key and node/path except when intentionally switching between staging and production
- Firebase REST wrap/unwrap format
- `fbUrl()` behavior
- `fbRead()` / `fbWrite()` data contract
- `applyRemoteState()` version synchronization
- `serializeState()` / `persistNow()` version behavior
- existing state shape: `tabs`, `tasks`, `timestamps`, `tabCols`, `tabBuckets`
- current task field names
- current local cache keys
- current user/session identity behavior

### Never change the Firebase data format casually
The app relies on this wrapped Firebase format:

```javascript
{ d: JSON.stringify(state), v: versionNumber, ts: isoString, savedBy: userName }
```

Any change to this is a database migration, not a normal bug fix.

### Prefer surgical changes over elegance
This project is a working internal tool. Stability matters more than code elegance.

A small, slightly repetitive patch is better than a broad refactor that risks losing functionality.

---

## Pre-Change Checklist

Before making code changes, the AI/dev should answer these internally or in the response:

1. What exact bug or feature is being addressed?
2. What version/file is the current base?
3. Which functions/sections will be touched?
4. Which sensitive areas must not be changed?
5. Does this require Firebase changes, or is it UI/state-only?
6. Does the change require a state/data migration? If yes, stop and explain before editing.

---

## Post-Change Checklist

After making code changes:

1. Bump `APP_VERSION`.
2. Confirm the environment label is visible and accurate.
3. Confirm `DATA_NODE` and `STREAM_NODE` match the intended environment.
4. Add a clear changelog entry.
5. Update this handoff if the change affects workflow, architecture, known bugs, or pending features.
6. Run a basic JavaScript syntax check if possible.
7. Summarize:
   - what changed
   - what was not changed
   - what Kyle should test next
6. Save the final files with clear names.

Recommended test pattern after each app update:

1. Load the app locally or on GitHub Pages.
2. Add a test task.
3. Refresh and confirm it persists.
4. Edit the task and confirm the edit persists.
5. Test from another PC/browser if the change touched sync or Firebase.
6. Confirm the “Load updates” banner appears/clears if sync behavior was touched.

---

## Version Naming / File Handling Recommendation

The app should normally be returned as:

```text
index.html
```

Kyle will manage folders/repos manually outside the AI chat. Do not assume separate permanent filenames like `index.production.html` or `index.staging.html` unless Kyle explicitly requests them.

For support docs, use clear versioned filenames:

```text
GI_TaskTracker_CHANGELOG_v12_0.md
GI_TaskTracker_HANDOFF_v12_0.md
```

After Kyle confirms a staging build works, the production/live release should be created by changing only the environment label and the two Firebase node lines unless the release task explicitly requires more.

Then use the newly confirmed files as the base for the next session.




---

## Production vs Staging Firebase Environments

### Goal
Use GitHub Pages as the safe **staging/test app shell** and the company-hosted site as the stable **production/live app shell**.

The app is a static HTML file. Any hosted copy of the same `index.html` can read/write the same Firebase data if it uses the same Firebase project, database URL, API key, and data path. This is useful, but dangerous during development because a broken test build can still modify live data.

### Recommended setup
Do **not** create a second full Firebase project unless there is a strong reason. The preferred approach is to use two separate data paths inside the same Realtime Database:

```javascript
// Production / live users
var FB_PATH = 'tasktracker/state';

// Staging / GitHub test build
var FB_PATH = 'tasktracker_staging/state';
```

This keeps the Firebase project and credentials the same while separating live data from test data.

### Visible environment/version label requirement
Every generated HTML build must make the connected Firebase path visible near the top of the app, close to the normal version/status area. This is required for both staging and production builds.

Recommended labels:

```text
v11.7 — STAGING — tasktracker_staging/state
v11.7 — PRODUCTION — tasktracker/state
```

This requirement exists so Kyle and other users can immediately see whether the build is connected to test data or live company data. Do not rely on filename alone, browser URL alone, or memory.

If the app has a single `APP_VERSION` display string, update it to include the environment. If the app has separate header/status elements, keep `APP_VERSION` clean if preferred, but add an adjacent visible environment/path label.

### Optional true second database instance
Firebase also supports multiple Realtime Database instances in the same project, but Firebase documentation notes that multiple database instances require the project to be on the Blaze pay-as-you-go plan. Use this only if separate paths are not enough.

If a true second database instance is used, the staging build must change `FB_DB_URL` to the staging database URL. If only a second path is used, `FB_DB_URL` stays the same and only `FB_PATH` changes.

### Production/staging rule
Future AI sessions must identify which environment the output file is intended for before changing Firebase settings:

| Environment | Hosted where | Firebase target | Required visible label/header | Purpose |
|---|---|---|---|---|
| Production | Company website | `tasktracker/state` | `PRODUCTION — tasktracker/state` | Stable version used by other GI users |
| Staging | GitHub Pages | `tasktracker_staging/state` | `STAGING — tasktracker_staging/state` | Testing new versions before promotion |

The app's top visible version/status area must make the connected environment obvious. Users should never have to inspect code to know whether they are editing live production data or staging/test data.

### Safe promotion workflow
1. Make changes in a fresh AI session using the current working files.
2. Generate a staging HTML build first.
3. Confirm the staging build uses `tasktracker_staging/state`.
4. Confirm the top visible version label/header clearly says `STAGING — tasktracker_staging/state`.
5. Upload staging build to GitHub Pages and test there.
6. Test add/edit/delete/refresh and, if relevant, multi-PC merge behavior.
7. After staging is approved, create or update a production build that changes only the Firebase target back to `tasktracker/state` and updates the visible label/header to `PRODUCTION — tasktracker/state`.
8. Upload the production build to the company website.
9. Update the changelog and handoff with what was promoted.

### Hard guardrail
Never test Firebase, merge, delete, migration, field/section reorder, or state-shape changes against production data first.

UI-only changes may be previewed against production data if needed, but staging is still preferred.

### AI instruction for future sessions
When making app changes, the AI/dev must:

1. Ask or infer whether the requested output is a **staging build**, **production build**, or **both**.
2. Preserve the Firebase project and database format unless the requested change specifically requires otherwise.
3. Use `tasktracker_staging/state` for GitHub/test builds unless Kyle explicitly says to use production data.
4. Use `tasktracker/state` only for the final production/company-site build.
5. Clearly state which Firebase path each generated HTML file uses.
6. Update the app's top visible version/status label so it includes both the app version and the connected environment/path.
7. Never silently change `FB_DB_URL`, `FB_PATH`, `fbRead()`, `fbWrite()`, `serializeState()`, `persistNow()`, or the wrapped Firebase format.

### Recommended file naming for environment builds
Use explicit environment names so the wrong file is not uploaded accidentally:

```text
gi_task_tracker_v11_7_staging.html
gi_task_tracker_v11_7_production.html
GI_TaskTracker_CHANGELOG_v11_7.md
GI_TaskTracker_HANDOFF_v11_7.md
```

When uploading to hosts:

```text
GitHub Pages staging file -> upload/rename as index.html in the GitHub repo
Company production file -> upload/rename as index.html on the company website
```

### Seeding staging data
To make staging realistic, copy the current production data from:

```text
tasktracker/state
```

to:

```text
tasktracker_staging/state
```

Do this manually in the Firebase console using export/import or copy/paste JSON. After seeding, test changes only in staging until the build is ready for production.


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
