# GI Task Tracker — Changelog

---

## v12.5.2 — 2026-05-10
**STAGING FIX + LIVE-PROMOTION READY — Report Button Cleanup + Changelog Click Fix**

Kyle requested that the Report button not appear both globally and on each PM/person tab, and noted that the version label still triggered the five-click admin prompt but did not show the changelog on a normal single click.

### Changed
- Changed staging `APP_VERSION` to `v12.5.2 — STAGING — tasktracker_staging/state`.
- Removed the duplicate Report button from each individual PM/person tab header.
- Kept report access in the main top header.
- Kept the Dashboard report button as `Generate Report`.
- Fixed the in-app changelog modal open behavior by using the existing modal `open` class instead of the unused `show` class.
- Added a v12.5.2 entry to the in-app `APP_CHANGELOG` array.

### Preserved
- Five rapid clicks on the version label still triggers the admin unlock flow.
- Backup / Restore buttons remain hidden unless admin tools are unlocked.
- Dashboard report generation remains available.
- Sticky navigation, inline double-click hint, Task Sections, deletion logging, subtasks, and prior staging features remain unchanged.

### Live-promotion file prepared
- A live-ready file was also prepared with:

```javascript
var APP_VERSION = 'v12.5.2 — LIVE — tasktracker/state';
var DATA_NODE   = 'tasktracker/state';
var STREAM_NODE = 'tasktracker/state';
```

### Validation
- Extracted the JavaScript from staging and live-ready builds and ran `node --check`; syntax passed.

---

## v12.5.1 — 2026-05-10
**STAGING ONLY — Inline Double-Click Hint Polish**

Kyle noticed that the Customer column still looked visually odd on hover because the `double-click to edit` wording appeared as an added line below the customer/task name. The hint is important and should remain, but it should appear on the same top line as the customer/task name.

### Changed
- Changed `APP_VERSION` to `v12.5.1 — STAGING — tasktracker_staging/state`.
- Kept both Firebase node variables on staging/testing:

```javascript
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

- Removed the old hover pseudo-element that appended `double-click to edit` to the first table cell.
- Added an inline `dbl-edit-hint` span inside the Customer/task-name line.
- The hint now appears to the right of the customer/task name on desktop hover instead of creating a new line underneath it.
- Applied the same inline hint behavior to normal task rows and subtask rows.

### Not changed
- No live/production build was created.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No task, subtask, section, audit log, dashboard, report, backup, restore, sticky navigation, in-app changelog, or production-promotion behavior changes.

### Validation
- Extracted the JavaScript and ran `node --check`; syntax passed.

---

## v12.5 — 2026-05-10
**STAGING ONLY — Sticky Navigation + Customer Column + In-App Changelog**

Kyle requested three usability improvements: make the Customer column a little wider so it does not constantly resize on hover, keep the top navigation visible while scrolling, and make the Task Tracker revision clickable so site/app changes can be viewed inside the app.

### Changed
- Changed `APP_VERSION` to `v12.5 — STAGING — tasktracker_staging/state`.
- Kept both Firebase node variables on staging/testing:

```javascript
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

- Moved the tab/navigation bar into the sticky header area so Dashboard, My Tasks, and PM tabs remain visible while scrolling.
- Added a dedicated Customer column class and widened it to reduce frequent hover/resize behavior.
- Made the visible Task Tracker version label clickable.
- Added an in-app **Site / App Changelog** modal showing recent app changes by version.
- Preserved the hidden admin Backup / Restore unlock behavior on the version label:
  - Normal click opens the changelog.
  - Five rapid clicks still triggers the admin unlock prompt for Kyle/Kyle Harmer.

### Not changed
- No live/production build was created.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No task, subtask, section, audit log, dashboard, report, backup, restore, or production-promotion behavior changes.

### Validation
- Extracted the JavaScript and ran `node --check`; syntax passed.

---

## v12.4 — 2026-05-10
**STAGING ONLY — Reorderable Task Sections + Broader Deletion Logging**

Kyle requested that each person's individual tab Configure panel allow the task group order to be changed, that “buckets” have a better user-facing name, that default task groups be deletable, and that deletions be tracked somewhere.

### Changed
- Changed `APP_VERSION` to `v12.4 — STAGING — tasktracker_staging/state`.
- Kept both Firebase node variables on staging/testing:

```javascript
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

- Renamed the Configure panel's visible “Task Buckets” wording to **Task Sections**.
- Added reorder controls for Task Sections inside each individual tab's Configure modal:
  - Drag-and-drop rows on desktop.
  - Up/down arrow buttons for desktop, mobile, and fallback use.
- Section order now persists through `state.tabBuckets[tab]` and drives the order displayed on that person's tab.
- Default sections can now be deleted, not just custom-added sections.
- Section deletion now always asks for confirmation and warns when tasks will be removed with that section.
- Added `state.auditLog` to track deletions and preserved it through Firebase save/load/merge.
- Added a **Recent Delete Log** section inside Configure showing recent deleted tasks, subtasks, sections, columns, and tabs for that tab.
- Added deletion logging for:
  - Tasks
  - Subtasks
  - Task Sections
  - Custom columns
  - Entire tabs

### Not changed
- No live/production build was created.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No task/subtask schema changes beyond preserving the new `auditLog` state field.
- No dashboard, report, backup, restore, mobile action-sheet, or production-promotion behavior changes.

### Validation
- Extracted the JavaScript and ran `node --check`; syntax passed.

---

## v12.3.2 — 2026-05-09
**STAGING ONLY — More Obvious Add Subtask UI**

Kyle confirmed the nested subtask feature works, but noted that adding a subtask was too subtle.

### Changed
- Changed `APP_VERSION` to `v12.3.2 — STAGING — tasktracker_staging/state`.
- Added an always-visible `+ Add subtask` pill button directly under the parent task name.
- Changed the parent-row action icon from a subtle arrow-only button to a clearer `+ Sub` button.
- Added `Add Subtask` to the mobile action sheet for parent tasks.
- Hid the mobile `Add Subtask` action when the selected row is already a subtask, preserving the single-level nesting rule.

### Not changed
- No live/production build was created.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No task/subtask schema changes.
- No report, dashboard, sync, merge, backup, restore, or configuration behavior changes.

### Validation
- Extracted the JavaScript and ran `node --check`; syntax passed.

---

## v12.3.1 — 2026-05-09
**STAGING ONLY — Startup Fix for Nested Subtasks Build**

Kyle reported that the v12.3 staging file did not load the visible version number or connect/render after upload.

### Fixed
- Corrected a startup-breaking JavaScript reference in the new subtask row rendering path.
- The Edit button builder for normal task rows accidentally referenced `ref.parentId` inside `renderTaskRow()`, where `ref` does not exist.
- Replaced that with the already-available `parentId` value so both normal tasks and subtasks render safely.
- Moved `updateUserChip()` earlier in `finishInit()` so the visible version/environment label is written before the main task render path. This makes future startup/render issues easier to see.

### Preserved
- Single-level nested subtasks from v12.3.
- Same-column subtask form behavior.
- Staging-only Firebase path:

```javascript
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

### Validation
- Extracted the JavaScript and ran `node --check`; syntax passed.
- Ran a mocked startup/render pass; no immediate startup exception occurred.

---

## v12.3 — 2026-05-09
**STAGING ONLY — Single-Level Nested Subtasks**

Kyle requested a way to create a nested subtask under a main task, with the same column options and behavior as normal tasks, but limited to a single nested level.

### Changed
- Generated this build as staging-only, not live.
- Changed `APP_VERSION` to `v12.3 — STAGING — tasktracker_staging/state`.
- Kept both Firebase node variables on staging/testing:

```javascript
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

- Added a single-level `subtasks` array to parent task records.
- Added an Add Subtask row action on top-level tasks.
- Reused the existing task form for subtasks so subtasks use the same configured columns, statuses, priorities, due dates, owners, supporting names, milestone, notes, and meeting notes.
- Added edit, delete, and change-history support for subtasks.
- Prevented infinite nesting by only showing Add Subtask on top-level tasks, not on subtasks.
- Rendered subtasks directly beneath their parent task with an indented visual style and a Subtask label.
- Added a parent task subtask-count chip when a task has one or more subtasks.
- Included subtasks in tab statistics, dashboard totals, My Tasks results, all-task dashboard filtering, and CSV exports.
- Updated CSV exports to include Task Type and Parent Task columns.
- Updated merge handling so subtasks can participate in sync/merge without being blindly flattened into unrelated top-level tasks.

### Not changed
- No production/live file was generated or promoted.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No infinite nesting.
- No unrelated backup/restore, mobile action-sheet, tab, bucket, dashboard, report default, or Firebase environment behavior changes.

### Test notes
- Upload the generated `index.html` to the staging GitHub Pages repo only.
- Confirm the visible label says `v12.3 — STAGING — tasktracker_staging/state`.
- On a top-level task row, click the curved Add Subtask action and create a subtask.
- Confirm the subtask appears nested directly beneath the parent task.
- Confirm the subtask can be edited, deleted, and viewed in change history.
- Confirm subtasks do not show their own Add Subtask button.
- Confirm due date, status, priority, owner/supporting, and custom visible columns behave the same as normal tasks.
- Confirm dashboard, My Tasks, and CSV exports include subtasks.

---


## v12.2 — 2026-05-09
**STAGING ONLY — Mobile Action Sheet / Windows Touch Detection Fix**

Kyle reported that on mobile, tapping a task opened the correct action sheet, but tapping Edit / History / Delete did not perform the action. He also reported that some Windows 10 desktop systems/browsers were incorrectly being treated as mobile/touch devices.

### Changed
- Generated this build as staging-only, not live.
- Changed `APP_VERSION` to `v12.2 — STAGING — tasktracker_staging/state`.
- Changed both Firebase node variables to staging/testing:

```javascript
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

- Replaced broad touch detection with `isMobileActionSheetMode()`, which only uses the mobile action sheet for true mobile/tablet-style behavior.
- Avoided treating Windows touch-capable desktops as mobile just because `maxTouchPoints` is present.
- Fixed action sheet button handlers so Edit / History / Delete save the selected task ID and bucket before closing the sheet.
- Updated row interaction wiring so:
  - Mobile/tablet layouts use single-tap action sheet behavior.
  - Desktop and hybrid Windows devices use double-click-to-edit behavior.

### Not changed
- No production/live file was generated or promoted.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No task schema, sync, merge, report, dashboard, tab, or configuration behavior changes.
- No backup/restore admin behavior changes.

### Test notes
- On a phone, tap a task row; the action sheet should open.
- In that sheet, Edit Task should open the edit modal, View Change Log should open history, and Delete Task should run the delete confirmation.
- On Windows 10 desktop browsers, a normal click should not open the mobile action sheet.
- On Windows 10 desktop browsers, double-clicking a task row should open edit.
- Row action icons should continue to work directly.

---

## v12.1 LIVE — 2026-05-08
**Hidden Admin-Only Backup / Restore Controls — PROMOTED TO LIVE / TASK COMPLETE**

Kyle confirmed the v12.1 staging behavior looked good and requested promotion to the live version. This task is now complete.

### Live release changes
- Promoted the tested v12.1 Backup / Restore hiding behavior to the live build.
- Changed `APP_VERSION` to `v12.1 — LIVE — tasktracker/state`.
- Changed both Firebase node variables to production/live:

```javascript
var DATA_NODE   = 'tasktracker/state';
var STREAM_NODE = 'tasktracker/state';
```

### Preserved from tested staging build
- Backup and Restore remain hidden from normal users.
- Admin unlock remains: user name must be `Kyle` or `Kyle Harmer`, click the version/environment label five times, then enter the admin unlock code.
- Backup and Restore appear only for the current page session after unlock.
- `downloadBackup()` and `openRestoreDialog()` remain guarded by the admin-unlock check.
- Restore still requires a second confirmation warning before file selection.

### Not changed during promotion
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No task schema, sync, merge, report, dashboard, tab, or configuration behavior changes.
- No unrelated UI changes.

---

## v12.1 — 2026-05-08
**Hidden Admin-Only Backup / Restore Controls — STAGING**

This staging release hides the Backup and Restore controls so normal users cannot accidentally click them once the app is live.

### Changed
- Removed the normal visible Backup and Restore buttons from the main header.
- Moved Backup and Restore into a hidden admin tools container.
- Added a hidden unlock gesture on the version/environment label: click the small version text five times, while the current user name is `Kyle` or `Kyle Harmer`, then enter the admin unlock code.
- Added runtime guards to both `downloadBackup()` and `openRestoreDialog()` so the functions cannot run unless admin tools are unlocked.
- Added a second Restore confirmation warning before selecting a backup file.
- Bumped `APP_VERSION` to `v12.1 — STAGING — tasktracker_staging/state`.

### Not changed
- Firebase project URL/API key.
- `DATA_NODE` / `STREAM_NODE`; both remain on `tasktracker_staging/state`.
- Firebase wrapped data format.
- Main task schema, sync, merge, report, dashboard, tab, or configuration behavior.

### Test notes
- Normal load should show no Backup or Restore buttons.
- Report and Add PM Tab should still show normally.
- With user name set to `Kyle` or `Kyle Harmer`, clicking the small version label five times should prompt for the admin unlock code and reveal Backup / Restore for the current page session only.
- Restore should require both warning confirmations before file selection.

---

## Documentation Update — 2026-05-08
**Confirmed GitHub Testing Split + DATA_NODE/STREAM_NODE Release Workflow**

This documentation update records that the production/testing split is now confirmed and updates future AI instructions to match the actual app variables.

### Confirmed by Kyle
- Testing/staging GitHub Pages URL is: `https://kyleharmer.github.io/gi-tracker-testing/`.
- Testing/staging Firebase branch/path is: `tasktracker_staging/state`.
- Production/live GitHub Pages URL remains: `https://kyleharmer.github.io/gi-tracker/`.
- Production/live Firebase branch/path remains: `tasktracker/state`.
- The app uses `DATA_NODE` and `STREAM_NODE`, not `FB_PATH`.
- The GitHub/database split worked and is correct.

### Workflow clarified
- Future AI/code sessions should assume all work is being done on staging/testing unless Kyle explicitly asks for a production/live release.
- Staging/testing builds must use:

```javascript
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

- Production/live release builds must use:

```javascript
var DATA_NODE   = 'tasktracker/state';
var STREAM_NODE = 'tasktracker/state';
```

- `DATA_NODE` and `STREAM_NODE` must always point to the same environment.
- The visible top version/environment label must show which environment/database branch is connected.
- Kyle does not plan to use separate permanent names like `index.production.html` and `index.staging.html`; he will organize folders/repos manually, and AI should return the appropriate `index.html` for the requested environment.

### Important note
This is a documentation/process update only. It does not change the app code or bump `APP_VERSION`.

---

## Documentation Update — 2026-05-08
**Visible Environment/Database Label Requirement Added**

This documentation update adds a guardrail for GitHub staging builds vs company production builds.

### Added
- Future generated HTML builds must show the connected Firebase environment/path in the visible top version/status area.
- Staging/GitHub builds should visibly show: `STAGING — tasktracker_staging/state`.
- Production/company-site builds should visibly show: `PRODUCTION — tasktracker/state`.
- Safe promotion workflow now requires checking the visible label before uploading to GitHub or the company website.
- Handoff notes now record that Kyle successfully created the `tasktracker_staging/state` Firebase branch and confirmed the current live site still loads/connects.

### Important note
This is a documentation/process update only. It does not change the app code or bump `APP_VERSION`.

---

## Documentation Update — 2026-05-08
**Production/Staging Workflow Added**

This documentation update adds guidance for separating test builds from the live GI Task Tracker data.

### Added
- Recommended GitHub Pages as the staging/test app shell and the company website as the production/live app shell.
- Recommended using a separate Firebase Realtime Database path for staging:
  - Production: `tasktracker/state`
  - Staging: `tasktracker_staging/state`
- Added a safe promotion workflow: test on staging first, then create/promote a production build only after approval.
- Added AI guardrails requiring future sessions to identify whether generated HTML is staging, production, or both.
- Added explicit warning not to test Firebase, merge, delete, migration, field/section reorder, or state-shape changes against production data first.
- Added environment-specific file naming recommendations.

### Important note
This is a documentation/process update only. It does not change the app code or bump `APP_VERSION`.

---

## v11.6 — 2026-05-07
**Confirmed Working Sync/Merge Recovery**

This release resolves the broken v11.x save/sync path by rebuilding from the confirmed-working v10 file instead of continuing from the incomplete v11.5 shell.

### User-confirmed status
Kyle tested the fixed build and confirmed:

- Tasks save correctly.
- Tasks persist after browser refresh.
- Merge behavior appears to work locally.
- Updates/sync appear to work across other PCs.

### Actual root cause
The uploaded `v11_5` file was missing most of the main application JavaScript. It included the Firebase/sync shell, but not the full v10 app code needed for normal operation.

Missing or effectively unavailable pieces included core items such as:

- `state`
- default column/bucket definitions
- `initDefault()`
- `renderApp()`
- modal/task edit functions
- reporting/dashboard/config logic
- other main UI functions

Because of this, the app could connect to Firebase but fail during initialization/rendering when it attempted to call or reference missing app functions and variables.

### Fix strategy
- Rebuilt `v11.6` from the known-working v10 file.
- Reapplied only the intended v11 sync improvements surgically.
- Avoided using `v11_5` as the base.

### Changes included
- `APP_VERSION` bumped to `v11.6`.
- Preserved v10's working Firebase REST URL behavior, including `?auth=`.
- Preserved v10-compatible version bump behavior.
- Preserved the Firebase `{ d: JSON.stringify(state) }` write format.
- Preserved Firebase read unwrap behavior.
- Preserved/added SSE unwrap behavior for incoming stream updates.
- Added/restored `mergeStates()` for conflict-safe sync.
- Updated `acceptUpdate()` so loading remote updates merges rather than blindly overwrites local state.
- Added a visible `⚠` conflict badge for tasks that need review after a merge.
- Updated `saveTask()` so saving a task clears prior conflict flags.

### Not changed
- Core v10 app structure
- Existing task/state schema
- Main UI layout
- Modal/reporting/config behavior beyond the required sync conflict indicators
- Firebase database path or project credentials

---

## v11.5 — 2026-05-07
**Broken / Do Not Use as Base**

This file should not be used for future development.

### Issue
`v11_5` was incomplete. It contained the Firebase/sync shell and some interaction code, but was missing the main application JavaScript from v10.

### Result
Likely runtime failures included:

```javascript
ReferenceError: state is not defined
ReferenceError: initDefault is not defined
ReferenceError: renderApp is not defined
```

### Resolution
Do not patch from `v11_5`. Use `v11.6` or later as the base.

---

## v11.3 — 2026-05-07
**Superseded Firebase Write/Read Diagnosis**

Earlier diagnosis focused on REST `?auth=` behavior and version bump logic as possible causes of disappearing tasks and missing update banners.

### Later finding
The later v11.6 repair showed that the more important issue in the uploaded v11.5 file was structural: the file was missing the main app JavaScript.

### Important correction
The confirmed-working v10 file uses `?auth=` in REST calls successfully for this Firebase project. Therefore, removing `?auth=` should not be treated as a required fix for this app.

### Status
Superseded. Do not use v11.3 as the development base.

---

## v11.2 — 2026-05-07
**Real-time Merge (Conflict-Safe Sync) — Concept Carried Forward**

Replaced the previous overwrite-style update behavior concept with a field-level merge system. The intended behavior was carried forward into v11.6.

### Intended merge rules
| Scenario | Behavior |
|---|---|
| Task added on PC1 only | Pulled into PC2 on merge |
| Task added on PC2 only | Kept locally and not overwritten by remote |
| Same task edited on both | Field-by-field merge where possible |
| Task deleted remotely, exists locally | Restored/flagged instead of silently lost |
| Task deleted locally, not in remote | Local deletion intent respected where possible |

### Carried forward into v11.6
- `mergeStates()`
- merge-based `acceptUpdate()`
- conflict badge concept
- conflict-clearing behavior on task save

---

## v11.1 — 2026-05-07
**Firebase Connection Fix — Concept Carried Forward**

Fixed/identified Firebase wrap/unwrap regressions introduced during the v11 rebuild.

### Relevant patterns
- `fbRead()` needs to unwrap `{ d: "..." }` payloads.
- `fbWrite()` needs to write `{ d: JSON.stringify(data) }`.
- SSE stream events need to unwrap the payload before checking for `remote.tabs`.

### Status
Concept carried forward into v11.6.

---

## v11.0 — Initial rebuild
Full rebuild from modular source files (`gi_firebase_rest.js` + `gi_main_v4c.js`). This rebuild path led to the later incomplete/broken v11 series and should not be used as the base without careful comparison to v10.

---

## v10.0 — Last stable pre-rebuild
Working reference version. Firebase REST layer confirmed functional. Used as the base for the v11.6 recovery build.
