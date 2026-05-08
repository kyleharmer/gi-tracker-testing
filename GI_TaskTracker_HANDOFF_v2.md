# GI Task Tracker — Developer Handoff v2
*Continuation from previous session — for new Claude chat*

---

## Current State Summary

**Live URL:** https://kyleharmer.github.io/gi-tracker  
**GitHub repo:** kyleharmer/gi-tracker  
**Upload file as:** `index.html`

**Latest working version on GitHub:** v11.3 (but has bugs — see below)  
**Confirmed working reference:** v10 (`gi_task_tracker_firebase10.html`)

---

## ⚠️ ACTIVE BUG — Fix This First

### Symptom
- Tasks disappear on browser refresh
- "Load updates" banner never appears when another PC saves
- v10 saves and loads correctly; ALL v11.x versions do NOT

### What We Know For Certain
1. **v10 works** — tasks persist, sync works
2. **v11.0 through v11.3 are all broken** — tasks do NOT persist
3. The break was introduced in the v11 rebuild from source JS files
4. The app loads and shows "Synced" (Firebase connection is fine)
5. The console shows: `Loaded from Firebase: 8 tabs, v=17` on load — so fbRead() works
6. But after adding a task and refreshing, the task is gone — so fbWrite() is failing silently OR writing but not being read back

### Critical Investigation Needed
The EXACT difference between v10 and v11 in the write/read cycle. Here is what we know:

**v10 fbUrl (WORKS):**
```javascript
function fbUrl(node) {
  return FB_DB_URL + '/' + node + '.json?auth=' + FB_API_KEY;
}
```

**v11.3 fbUrl (we "fixed" this by removing auth= — but this may have been WRONG):**
```javascript
function fbUrl(node) {
  return FB_DB_URL + '/' + node + '.json';
}
```

**IMPORTANT:** v10 has `auth=` in fbUrl AND works for REST. We removed it in v11.3 thinking it was the issue. But v10 proves auth= on REST is fine for this Firebase project. The SSE 401 error was SSE-specific. We may have accidentally introduced another regression in v11.3.

**v10 serializeState:**
```javascript
function serializeState() {
  return {
    v: (localVersion || 0) + 1,  // increments here
    ts: new Date().toISOString(),
    savedBy: currentUser || 'anonymous',
    tabs: state.tabs, tasks: state.tasks,
    timestamps: state.timestamps,
    tabCols: state.tabCols, tabBuckets: state.tabBuckets
  };
}
```

**v10 persistNow:**
```javascript
function persistNow() {
  var payload = serializeState();
  payload.v = (localVersion || 0) + 1;  // increments AGAIN (double bump) — but v10 WORKS with this
  ignoreNextStream = true;
  fbWrite(payload)
    .then(function() {
      localVersion = payload.v;
      cacheSave(payload);
      ...
    })
```

**KEY INSIGHT:** The double version bump exists in v10 AND v10 works. So double bump is NOT the bug. Reverting our v11.3 "fix" to serializeState may be needed.

### Most Likely Real Bug Location
Something in the **initApp flow** is different between v10 and v11. Specifically:

In v10 `initApp`, when Firebase has data:
```javascript
fbRead().then(function(remote) {
  if (remote && remote.tabs && remote.tabs.length) {
    applyRemoteState(remote);
    cacheSave(remote);   // ← caches the REMOTE state (with correct v number)
    localVersion = remote.v;  // ← SETS localVersion from cloud
    ...
  }
})
```

Check if v11 `initApp` properly sets `localVersion = remote.v` after loading. If localVersion stays at 0 after load, then every save writes v=1 to Firebase, but Firebase already has v=17. Firebase might reject writes where v is lower than existing, or the SSE comparison `remote.v > localVersion` breaks.

**Also check:** Does v11 `applyRemoteState` set `localVersion` and `cloudVersion`?

In v10 `applyRemoteState`:
```javascript
function applyRemoteState(remote) {
  ...
  cloudVersion = remote.v || 0;
  localVersion = cloudVersion;  // ← syncs localVersion to cloud
  ...
}
```

If this is correct in v11, then `localVersion` should be set to 17 after load. Then `persistNow` would write v=18. That should work.

### Next Debug Steps for New Session
1. Upload `gi_task_tracker_firebase10.html` (v10) and `gi_task_tracker_v11_3.html` (latest)
2. Add console.log statements to the v11.3 copy:
   - In `initApp` after fbRead: log `localVersion`
   - In `persistNow` before fbWrite: log `payload.v` and `JSON.stringify(payload).substring(0,200)`
   - In `fbWrite` response handler: log the response
3. Test in browser with DevTools open and compare v10 vs v11.3 console output
4. The difference will be visible

### Alternative Approach
If the above is taking too long: **do a surgical line-by-line diff of ONLY the Firebase REST layer** (fbUrl, fbRead, fbWrite, initApp, applyRemoteState, serializeState, persistNow, cacheSave, cacheLoad) between v10 and v11.3. The bug is in one of those functions.

---

## Files Available

| File | Location | Notes |
|------|----------|-------|
| v10 (WORKING) | `/mnt/user-data/uploads/gi_task_tracker_firebase10.html` | Reference — do not modify |
| v11 (broken base) | `/mnt/user-data/uploads/gi_task_tracker_firebase11.html` | Original broken rebuild |
| v11.3 (latest, still broken) | `/mnt/user-data/outputs/gi_task_tracker_v11_3.html` | Has v11.1+v11.2+v11.3 patches |
| Changelog | `/mnt/user-data/outputs/GI_TaskTracker_CHANGELOG.md` | Full history |

---

## Firebase Credentials
```
Project ID:  gi-task-tracker
Database URL: https://gi-task-tracker-default-rtdb.firebaseio.com
API Key:      AIzaSyAHLU7QSQmaONIUAAVOt8mgzyYsLaMxFTw
Data path:    tasktracker/state
Stream path:  tasktracker/state (same, used for SSE)
Rules:        { ".read": true, ".write": true }
```

**Data format (NEVER change this):**
```javascript
// Write format:
{ d: JSON.stringify(state), v: versionNumber, ts: isoString, savedBy: userName }

// Read/unwrap:
if (raw.d && typeof raw.d === 'string') inner = JSON.parse(raw.d);
```

---

## Version History of Fixes Applied

### v11.1 — Firebase connection fix
- `fbRead()`: Added `{d:"..."}` unwrap logic
- `fbWrite()`: Added `{d: JSON.stringify(data)}` wrap logic
- SSE `put` handler: Added unwrap before checking `remote.tabs`

### v11.2 — Real-time merge (conflict-safe sync)
- Added `mergeStates()` function (field-level merge, deletion-loses rule)
- `acceptUpdate()`: now calls `mergeStates()` instead of overwriting
- Task rows: added `⚠` conflict badge
- `saveTask()`: clears conflict flag on save

### v11.3 — Attempted Firebase write fix (MAY HAVE INTRODUCED REGRESSION)
- `fbUrl()`: Removed `?auth=` param — **CAUTION: v10 has auth= and works. This change may be wrong.**
- `serializeState()`: Removed `+1` — **CAUTION: v10 has double bump and works. This change may be wrong.**
- These changes were based on incorrect diagnosis. Revert both if needed.

---

## Versioning Rules (IMPORTANT)
- Bug fixes → point release (v11.3 → v11.4)
- New features → major version (v11 → v12)
- Always bump `APP_VERSION` constant and add changelog entry

## Surgical Edit Rules (IMPORTANT)
- Before any change: state which functions will be touched and why
- After any change: state what was NOT changed
- Never rebuild unrelated sections
- Never change Firebase `{d: JSON.stringify(state)}` wrap format

---

## What Still Needs To Be Done

### Priority 1 (Fix the bug)
- Find and fix why tasks don't persist in v11.x
- Verify fix works on 2 PCs simultaneously
- Verify "Load updates" banner appears and merge works

### Priority 2 (Pending features from original handoff)
1. **Reorder Buckets and Columns in Configure panel**
   - Desktop: drag handle (⋮⋮) on each row
   - Mobile: Up/Down arrow buttons
   - Order persists to `state.tabBuckets[tab]` and `state.tabCols[tab]`

2. **Rename "Buckets" → "Sections", "Columns" → "Fields"** (pending Kyle's approval of terminology)

---

## People / Context
- **Kyle Harmer** — Ammunition PM at General Inspection LLC, built this tool
- **Chris Alexander** — Kyle's manager
- **Favs (Michael Del Favero)** — PM whose Excel frustration sparked this
- **Other PMs:** Noah McLaughlin, Kendra Fair, Jason McCowan, Service-Jonah, Design Bob & Cody
- **Management viewers:** Haliey Holmes + ~3 others
