# GI Task Tracker — Developer Handoff v12.5.2
*Continuation from previous session — includes v12.5.2 report button cleanup and changelog click fix, plus prior v12.5.1/v12.5 changes*

---

## Current State Summary

**Latest generated staging build:** `v12.5.2 — STAGING — tasktracker_staging/state`

This staging build removes the duplicate Report button from individual PM/person tabs and fixes the version-label changelog modal. A live-ready promotion file has also been generated but should only be uploaded to live after staging is verified.

### Staging build target

```javascript
var APP_VERSION = 'v12.5.2 — STAGING — tasktracker_staging/state';
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

### Live-ready promotion target

```javascript
var APP_VERSION = 'v12.5.2 — LIVE — tasktracker/state';
var DATA_NODE   = 'tasktracker/state';
var STREAM_NODE = 'tasktracker/state';
```

---

## v12.5.2 Change — Report Button Cleanup + Changelog Click Fix

### Purpose
Kyle noted that having a Report button at the top and again inside every PM/person tab was overkill. He wanted the duplicate tab-level buttons removed while keeping Report access at the top and on the Dashboard. He also found that the version label's five-click admin prompt worked, but a normal single click did not show the changelog.

### Behavior changed
- Removed the `hdr-report` button from the individual PM/person tab header.
- Removed the now-unused `hdr-report` click binding in `renderApp()`.
- Kept the global top-header Report button.
- Kept the Dashboard `Generate Report` button and binding.
- Fixed `openChangelog()` to add the modal `open` class instead of `show`, matching the app's existing modal CSS.
- Added a v12.5.2 entry to `APP_CHANGELOG`.

### Preserved
- Five rapid clicks on the version label still runs the admin unlock prompt.
- Backup / Restore remain hidden unless admin tools are unlocked.
- Dashboard report generation remains available.
- Existing report modal, report defaults, PDF generation flow, sticky navigation, customer column behavior, inline double-click hint, Task Sections behavior, deletion logging, and subtasks are unchanged.

### Files/functions touched
- `APP_VERSION`
- `APP_CHANGELOG`
- `renderApp()` tab header actions
- `openChangelog()`

### Suggested testing
1. Upload `index_v12_5_2_staging.html` as the staging `index.html`.
2. Confirm the visible version says `v12.5.2 — STAGING — tasktracker_staging/state`.
3. Confirm individual PM/person tabs no longer show a Report button in the tab header.
4. Confirm the main top-header Report button still opens the report modal.
5. Confirm Dashboard still shows `Generate Report` and opens the report modal.
6. Click the version label once and confirm the Site / App Changelog modal opens.
7. As Kyle/Kyle Harmer, rapidly click the version label five times and confirm the admin unlock prompt still appears.
8. Confirm Backup / Restore remain hidden unless admin tools are unlocked.
9. After staging is verified, upload `index_v12_5_2_LIVE.html` to the live GitHub Pages repo as `index.html`.
10. Confirm the live version label says `v12.5.2 — LIVE — tasktracker/state`.

### Validation
- JavaScript extraction and `node --check` passed for both staging and live-ready files.

---

## v12.5.1 Staging Change — Inline Double-Click Hint Polish

### Purpose
Kyle noticed that the Customer column still appeared to visually jump on hover because the `double-click to edit` hint appeared below the customer/task name. The hint is important for discoverability, so it should remain visible on hover, but appear on the same top line as the customer/task name.

### Build target

```javascript
var APP_VERSION = 'v12.5.1 — STAGING — tasktracker_staging/state';
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

### Change summary
- Removed the old `td:first-child::after` hover hint behavior.
- Added a dedicated inline `dbl-edit-hint` span in the Customer/task-name line.
- Added `.customer-line` styling so the customer/task name and hint sit on one line.
- The hint remains hidden until desktop hover, then appears to the right of the customer/task name.
- Applied this to both normal task rows and subtask rows.

### Files/functions touched
- Version line: `APP_VERSION`.
- `APP_CHANGELOG` array: added v12.5.1 entry.
- CSS near the desktop double-click hint styles.
- `renderTaskRow()` customer-column rendering for normal tasks and subtasks.

### Not changed
- No live/production build was created.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No task/subtask data schema changes.
- No sticky navigation, in-app changelog modal behavior, section ordering, deletion logging, dashboard, report, backup, restore, or production-promotion behavior changes.

### Validation
- JavaScript extraction and `node --check` passed.

---

## v12.5 Staging Change — Sticky Navigation + Customer Column + In-App Changelog

### Purpose
Kyle requested three app usability improvements:
- Make the Customer column slightly wider so it does not constantly resize on hover.
- Keep the top navigation visible while scrolling so users always know where they are and can jump to another tab easily.
- Make the Task Tracker revision/version clickable and show a changelog of site/app changes inside the app.

### Behavior added/changed
- The `tabsBar` navigation is now inside the sticky header container, so Dashboard, My Tasks, and each PM/person tab remain pinned while scrolling.
- The Customer column now receives a `cust-col` class in generated task tables and has a wider minimum/target width.
- The visible version label now uses an `app-ver-link` style and opens a new `Site / App Changelog` modal.
- Added an `APP_CHANGELOG` array in the app code with concise entries for recent versions.
- Added `openChangelog()` to render the changelog modal.
- Preserved the hidden admin unlock behavior:
  - A normal single click opens the changelog after a short delay.
  - Five rapid clicks still opens the admin unlock prompt for Kyle/Kyle Harmer.

### Files/functions touched
- Version/environment lines: `APP_VERSION` bumped to v12.5; `DATA_NODE` and `STREAM_NODE` remain staging.
- Header/nav markup: moved `tabsBar` into `.hdr`.
- CSS: added styles for `app-ver-link`, `cust-col`, and changelog modal entries.
- Modal markup: added `changelogModal`.
- JavaScript: added `APP_CHANGELOG`, `versionClickTimer`, `runAdminUnlockPrompt()`, `handleVersionClick()`, and `openChangelog()`.
- `updateUserChip()` now binds the version label click to `handleVersionClick()`.
- `renderTaskRow()` and the task table header generation now apply the `cust-col` class for the Customer column.

### Not changed
- No live/production build was created.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No state schema changes.
- No task/subtask, section reorder/delete, audit log, dashboard, report, backup, restore, mobile action-sheet, or production-promotion behavior changes.

### Suggested testing
1. Upload the generated `index.html` to the staging GitHub Pages repo only.
2. Confirm the visible label says `v12.5 — STAGING — tasktracker_staging/state`.
3. Scroll a long PM/person tab and confirm the top navigation stays visible.
4. Confirm the Customer column is wider and feels less jumpy on hover.
5. Click the version label once and confirm the Site / App Changelog modal opens.
6. With display user name set to `Kyle` or `Kyle Harmer`, rapidly click the version label five times and confirm the admin unlock prompt still appears.
7. Confirm normal Backup / Restore buttons remain hidden unless admin tools are unlocked.
8. Confirm existing v12.4 Task Sections reorder/delete/logging behavior still works.
9. Confirm subtasks from v12.3.x still add, edit, delete, and display correctly.

---

## Current State Summary

**Latest generated staging build:** `v12.4 — STAGING — tasktracker_staging/state`

This file is still staging/testing only. Do not promote to live until Kyle confirms the staging behavior works.

### Build target

```javascript
var APP_VERSION = 'v12.4 — STAGING — tasktracker_staging/state';
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

---

## v12.4 Staging Change — Reorderable Task Sections + Broader Deletion Logging

### Purpose
Kyle requested that each person's Configure panel allow the existing task “buckets” to be reordered, that “buckets” have a better visible name, that default buckets be deletable, and that deletions be tracked somewhere.

### User-facing terminology
- Internal code still uses `bucket` / `tabBuckets` to avoid a risky broad refactor.
- The Configure modal now uses **Task Sections** as the visible label.
- Add wording now says `+ Add Section`, and the input placeholder says `New section name`.

### Behavior added
- In each individual tab's Configure panel, Task Sections can now be reordered by:
  - Dragging rows in the Configure modal.
  - Using up/down arrow buttons beside each row.
- The order persists to `state.tabBuckets[tab]`.
- The persisted section order controls the display order of the sections on the tab.
- Default sections can now be deleted the same as custom sections.
- Section deletion always asks for confirmation. If the section contains tasks, the prompt warns that those tasks and any subtasks inside them will be deleted too.
- Added `state.auditLog` for deletion tracking.
- Added a `Recent Delete Log` panel inside Configure showing recent deleted items for that tab.

### Deletions now logged
- Normal task deletion.
- Subtask deletion.
- Task Section deletion.
- Custom column deletion.
- Whole-tab deletion.

### Files/functions touched
- Version/environment lines: `APP_VERSION` and `BUILD_DATE`; `DATA_NODE` and `STREAM_NODE` remain staging.
- CSS for Configure section drag handles, move buttons, helper text, and audit log display.
- `state` default now includes `auditLog: []`.
- Firebase state preservation updated in `serializeState()`, `applyRemoteState()`, and `mergeStates()` so `auditLog` is saved/loaded/merged.
- `renderCfgBody()` now renders Task Sections reorder controls, delete buttons for all sections, and Recent Delete Log.
- Added helpers: `moveBucket()`, `wireSectionDrag()`, `reorderBucketBefore()`, and `logAuditEvent()`.
- Updated `addBucket()`, `removeBucket()`, `resetBuckets()`, `removeCustomCol()`, `deleteTask()`, and `confirmDeleteTab()`.

### Not changed
- No live/production build was created.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No broad internal rename from bucket to section.
- No task/subtask field schema changes beyond preserving `auditLog` in state.
- No dashboard, report, backup, restore, mobile action-sheet, or production-promotion behavior changes.

### Suggested testing
1. Upload the generated `index.html` to the staging GitHub Pages repo only.
2. Confirm the visible label says `v12.4 — STAGING — tasktracker_staging/state`.
3. Open a normal person tab, click Configure, and confirm the old “Task Buckets” area now reads `Task Sections`.
4. Reorder sections with the up/down buttons and confirm the main tab order changes after closing/configuring.
5. Reorder sections by dragging a row and dropping it onto another row.
6. Delete a custom section and confirm it is removed.
7. Delete a default section and confirm it is removed.
8. Delete a section containing at least one task/subtask and confirm the warning is clear.
9. Confirm deleted tasks, subtasks, sections, custom columns, and tabs appear in the Recent Delete Log when applicable.
10. Confirm subtasks from v12.3.x still add, edit, delete, and display correctly.

---

## Current State Summary

**Latest generated staging build:** `v12.3.2 — STAGING — tasktracker_staging/state`

This file is still staging/testing only. Do not promote to live until Kyle confirms the staging behavior works.

### Build target

```javascript
var APP_VERSION = 'v12.3.2 — STAGING — tasktracker_staging/state';
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

---

## v12.3.2 Staging Change — More Obvious Add Subtask UI

### Status
Kyle confirmed the nested subtask feature works, but the Add Subtask entry point was too subtle. This is a staging-only UI polish build.

### Build target

```javascript
var APP_VERSION = 'v12.3.2 — STAGING — tasktracker_staging/state';
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

### Change summary
- Added an always-visible `+ Add subtask` pill directly under the parent task name.
- Changed the parent-row action button from an arrow-only icon to a clearer `+ Sub` button.
- Added `Add Subtask` to the mobile action sheet for parent tasks.
- The mobile Add Subtask action is hidden when the selected row is already a subtask, so nesting remains one level only.

### Not changed
- No live/production build was created.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No task/subtask schema changes.
- No report, dashboard, sync, merge, backup, restore, or configuration behavior changes.

### Validation
- JavaScript extraction and `node --check` passed.

---

## v12.3.1 Staging Fix — Startup/Render Issue

### Issue reported
Kyle reported that the v12.3 staging file did not load the version number or connect/render properly after upload.

### Root cause
The v12.3 nested subtask row renderer contained a bad reference inside `renderTaskRow()`: the Edit button markup referenced `ref.parentId`, but `ref` is not defined inside that function. This caused a JavaScript runtime exception during render, which made the app appear not to finish loading.

### Fix
- Replaced the bad `ref.parentId` reference with the function's existing `parentId` argument.
- Kept single-level subtask behavior unchanged.
- Moved `updateUserChip()` earlier in `finishInit()` so the version/environment label is populated before the main render path.

### Validation
- JavaScript extraction and `node --check` passed.
- A mocked startup/render pass completed without an immediate startup exception.

---

## Current State Summary

**Production/live URL:** https://kyleharmer.github.io/gi-tracker  
**Production GitHub repo:** `kyleharmer/gi-tracker`  
**Testing/staging URL:** https://kyleharmer.github.io/gi-tracker-testing/  
**Testing/staging GitHub repo:** `kyleharmer/gi-tracker-testing`  
**Upload file as:** `index.html` in whichever repo/folder Kyle is actively managing

**Latest live build:** `v12.1` / hidden admin-only Backup / Restore controls promoted to live; Backup/Restore hiding task complete  
**Latest generated staging build:** `v12.3.2` / more obvious Add Subtask UI for single-level nested subtasks  
**Prior generated staging build:** `v12.2` / mobile action sheet + Windows touch-detection fix  
**Confirmed working reference:** `v10` / `gi_task_tracker_firebase10.html`  
**Broken/incomplete file to avoid as a base:** `gi_task_tracker_v11_5.html`

**Production Firebase node/path:** `tasktracker/state`  
**Testing/staging Firebase node/path:** `tasktracker_staging/state`  
**Important code naming:** The app currently uses `DATA_NODE` and `STREAM_NODE`, not `FB_PATH`. Both must point to the same environment.

**Current v12.3.2 staging target:**

```javascript
var APP_VERSION = 'v12.3.2 — STAGING — tasktracker_staging/state';
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

---

## v12.3 Staging Change — Single-Level Nested Subtasks

### Status
This is a staging-only development build. Kyle explicitly requested nested subtasks under normal tasks with all the same column options, but not infinite nesting. Live/production should not be touched until Kyle confirms staging works and asks for promotion.

### Feature summary
- Parent tasks now support a `subtasks` array.
- Subtasks reuse the same form and configured columns as normal tasks.
- Subtasks support the same main field behavior: customer/title, details/notes, owner, supporting, priority, status, due date, milestone, and meeting notes.
- Subtasks support edit, delete, history/changelog, completion timestamp logic, due-date badges, priority pills, status pills, and owner/supporting chips.
- The UI only allows one nested level: parent task → subtask. Subtasks do not show an Add Subtask action.

### UI behavior
- Top-level task rows now include a curved Add Subtask action.
- Parent tasks with subtasks show a small subtask-count chip next to the customer/task name.
- Subtasks render directly below their parent as indented rows with a Subtask label.
- Mobile action-sheet and desktop double-click behavior were preserved for both tasks and subtasks.

### Data/reporting behavior
- Subtasks are counted in tab statistics and dashboard totals.
- Subtasks appear in My Tasks when the searched person is owner/supporting.
- Subtasks appear in dashboard filtering/searching.
- CSV exports now include `Task Type` and `Parent Task` columns so normal tasks and subtasks are distinguishable.
- Sync/merge indexing was updated so subtasks can be found by ID and new remote subtasks can merge into their parent rather than being blindly treated as unrelated top-level tasks.

### Files/functions touched
- Version/environment lines: `APP_VERSION` only; `DATA_NODE` and `STREAM_NODE` remain staging.
- Styling for `.subtask-row`, `.subtask-indent`, `.subtask-chip`, and `.subtask-count`.
- Task creation helper `mk()` now seeds parent tasks with `subtasks: []`.
- Added helpers: `getTaskRef()`, `flattenBucketTasks()`, `taskMatchesMainFilters()`, `taskHasMatchingSubtask()`, and `renderTaskRow()`.
- Updated `getStats()`, `renderApp()`, `openAddTask()`, `openEditTask()`, `saveTask()`, `deleteTask()`, `showHistory()`, `updateMyTasksTable()`, `exportCSV()`, `exportAllCSV()`, `getAllTasks()`, dashboard table rendering, action-sheet context handling, and row interaction wiring.
- Updated merge indexing to include subtasks.

### Not changed
- No live/production build was created.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No unlimited/infinite nested levels.
- No unrelated backup/restore, tab, bucket, report-default, Firebase environment, or production-promotion behavior changes.

### Suggested testing before future work
1. Upload the generated `index.html` to the staging GitHub Pages repo only.
2. Confirm the visible label says `v12.3 — STAGING — tasktracker_staging/state`.
3. Add a subtask under a normal task and confirm it appears indented below the parent.
4. Edit the subtask and confirm its status, due date, priority, owner/supporting, notes, and any configured columns save correctly.
5. Confirm subtask history opens correctly.
6. Delete a subtask and confirm only the subtask is removed.
7. Confirm deleting a parent task warns that its subtasks will also be deleted.
8. Confirm subtasks do not show an Add Subtask button.
9. Confirm dashboard, My Tasks, and CSV exports include subtasks.
10. Confirm mobile action-sheet and Windows desktop double-click behavior still work.

---

## v12.2 Staging Change — Mobile Action Sheet / Windows Touch Detection Fix

### Status
This is a staging-only development build. Kyle explicitly noted that multiple future changes will be made in chat sessions after this, so live/production should not be touched until he says otherwise.

### Build target
The generated `index.html` uses:

```javascript
var APP_VERSION = 'v12.2 — STAGING — tasktracker_staging/state';
var DATA_NODE   = 'tasktracker_staging/state';
var STREAM_NODE = 'tasktracker_staging/state';
```

### Issue reported
- On mobile, tapping a task row opened the action sheet correctly, but tapping Edit / History / Delete did not run the selected action.
- On some Windows 10 desktop systems and browsers, the app incorrectly identified the device as touch/mobile and used mobile tap behavior.

### Root cause
- `actionSheetEdit()`, `actionSheetHistory()`, and `actionSheetDelete()` called `closeActionSheet()` before checking `_asTaskId` and `_asBucket`. Because `closeActionSheet()` clears those variables, the selected action had no task context left to use.
- The prior detection used broad touch capability detection: `('ontouchstart' in window) || (navigator.maxTouchPoints > 0)`. Windows touch-capable desktops can report touch support even when they should still use desktop behavior.

### Fix summary
- Added `isMobileActionSheetMode()` and stopped using touch capability alone as the mobile/desktop decision.
- Mobile action sheet behavior now requires either a mobile user agent or a narrow/coarse/no-hover style layout.
- Desktop and hybrid Windows devices keep double-click-to-edit behavior.
- Action sheet button functions now cache the selected task ID and bucket before closing the sheet.

### Files/functions touched
- `APP_VERSION`, `BUILD_DATE`, `DATA_NODE`, and `STREAM_NODE` for staging.
- Replaced `isTouchDevice` with `isMobileActionSheetMode()`.
- Updated `actionSheetEdit()`, `actionSheetHistory()`, and `actionSheetDelete()`.
- Updated `wireRowInteractions()` row click/double-click behavior.

### Not changed
- No live/production build was created.
- No Firebase project URL/API key changes.
- No Firebase wrapped data format changes.
- No task schema, sync, merge, reporting, dashboard, tab, configuration, or backup/restore behavior changes.

### Suggested testing before future work
1. Upload the generated `index.html` to the staging GitHub Pages repo only.
2. Confirm the visible label says `v12.2 — STAGING — tasktracker_staging/state`.
3. On mobile, tap a task row and test Edit / History / Delete from the sheet.
4. On Windows 10 desktop browsers, confirm normal click does not open the action sheet and double-click opens edit.
5. Confirm row action icons still work normally.

## v12.1 Live Promotion — Backup / Restore Hiding Task Complete

### Status
Kyle confirmed the v12.1 staging behavior looked good and requested promotion to the live version. The Backup / Restore hiding task is complete.

### Live build target
The current live `index.html` should use:

```javascript
var APP_VERSION = 'v12.1 — LIVE — tasktracker/state';
var DATA_NODE   = 'tasktracker/state';
var STREAM_NODE = 'tasktracker/state';
```

### Behavior now expected in live
- Backup and Restore are hidden from normal users.
- The admin unlock remains documented for Kyle: set/display user name as `Kyle` or `Kyle Harmer`, click the version/environment label five times, then enter the admin unlock code.
- Backup and Restore appear only for the current page session after unlock.
- Restore requires the extra confirmation warning before the file picker opens.

### Promotion notes
Only the environment/version lines were changed from staging to live. Firebase project URL/API key, wrapped data format, app schema, sync/merge logic, report/dashboard/config behavior, and unrelated UI were intentionally left unchanged.

### Next-chat starting point
For the next task, upload these current files:

1. `index.html` — current live v12.1 build
2. `GI_TaskTracker_CHANGELOG_v12_1_live.md`
3. `GI_TaskTracker_HANDOFF_v12_1_live.md`

Default future development should still happen on staging/testing unless Kyle explicitly requests another live release.

---

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
