# GI Task Tracker — Changelog

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
