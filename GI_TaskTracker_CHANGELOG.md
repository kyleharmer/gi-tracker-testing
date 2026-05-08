# GI Task Tracker — Changelog

---

## v11.3 — 2025-05-07
**Firebase Write/Read Fix + Version Sync Fix**

Three bugs were causing tasks to disappear on refresh and the "Load updates" banner to never appear.

### Root causes

1. **`fbUrl()` had `?auth=` appended to REST calls** — Firebase open rules reject authenticated requests on open databases. Every `fbRead()` and `fbWrite()` call was silently failing. The SSE stream URL already had auth removed (v11.1 fix) but the REST URL was missed.

2. **Double version bump** — `serializeState()` was returning `localVersion + 1`, then `persistNow()` was also adding `+1` before writing. This caused version to jump by 2 on every save, breaking the SSE listener's `remote.v > localVersion` comparison — so the update banner never triggered.

3. **Consequence of bug 2** — because writes were failing (bug 1), tasks added locally were never persisted to Firebase, disappearing on refresh.

### Fix — 3 surgical changes
- `fbUrl()`: removed `?auth=` param from REST URL (open rules work without it)
- `serializeState()`: returns `localVersion` (no increment) — version source of truth
- `persistNow()`: keeps the single `+1` increment before writing — correct location
- `APP_VERSION`: bumped to v11.3

**Not touched:** `fbRead`, `fbWrite`, `attachStream`, `initApp`, `mergeStates`, `acceptUpdate`, all UI/modal/dashboard/config code

---

## v11.2 — 2025-05-07
**Real-time Merge (Conflict-Safe Sync)**

Replaced the previous "last writer wins" update behavior with a full field-level merge system. Clicking "Load updates" no longer overwrites local changes — all work on all devices is preserved.

### What changed
- **`mergeStates()`** — new function that merges remote state into local state using per-task, per-field logic
- **`acceptUpdate()`** — now calls `mergeStates()` instead of `applyRemoteState()` (overwrite)
- **Task row render** — tasks with unresolved conflicts show a yellow **⚠** badge next to the customer name; hovering shows which fields were merged
- **CSS** — added `.conflict-row` (subtle yellow background) and `.conflict-badge` styles
- **`saveTask()`** — clears the conflict flag and badge when a user opens and saves a flagged task

### Merge rules
| Scenario | Behavior |
|---|---|
| Task added on PC1 only | Pulled into PC2 on merge |
| Task added on PC2 only | Kept — not overwritten by remote |
| Same task edited on both | Field-by-field merge: most-recent changelog timestamp wins per field |
| Task deleted remotely, exists locally | Restored with ⚠ conflict flag — deletion does not win |
| Task deleted locally, not in remote | Stays deleted — local intent respected |

### Changelog entries added on conflict
Merged tasks get a system changelog entry: `⚠ Merge: remote update applied to [fields]` or `⚠ Conflict: this task was deleted remotely but has been restored.`

---

## v11.1 — 2025-05-07
**Firebase Connection Fix**

Fixed a regression introduced in the v11 rebuild where the app showed "Check Firebase rules" on load even with correct open rules.

### Root cause
Three pieces of the Firebase REST layer were accidentally dropped during the v11 rebuild from source files:

1. **`fbRead()`** — lost the `{d: "..."}` unwrap logic. Raw wrapped object was returned directly, making `remote.tabs` undefined, causing the app to think the DB was empty.
2. **`fbWrite()`** — lost the `{d: JSON.stringify(data)}` wrap logic. Raw state was written directly, causing Firebase to reject keys containing spaces and slashes.
3. **SSE `put` handler** — checked `msg.data.tabs` before unwrapping, so every stream event was silently dropped.

### Fix
Restored all three patterns to match the confirmed-working v10 implementation. No other code was changed.

---

## v11.0 — Initial rebuild
Full rebuild from modular source files (`gi_firebase_rest.js` + `gi_main_v4c.js`). See handoff document for full feature list.

---

## v10.0 — Last stable pre-rebuild
Working reference version. Firebase REST layer confirmed functional. Used as diff baseline for v11.1 fix.
