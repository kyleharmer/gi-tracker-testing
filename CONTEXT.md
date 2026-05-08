# GI Task Tracker — Project Context
**Version:** v11.6 | **Last updated:** 2026-05-07
**Live URL:** https://[your-github-username].github.io/gi-tracker/
**Firebase DB:** https://gi-task-tracker-default-rtdb.firebaseio.com
**Firebase API Key:** AIzaSyAHLU7QSQmaONIUAAVOt8mgzyYsLaMxFTw
**Data node:** `tasktracker/state`

---

## What this app is
A task tracker for a General Inspection team. It's a single-page web app (one `index.html` file) hosted on GitHub Pages. All data is stored and synced in real-time via Firebase Realtime Database — no backend server, no SDK, just plain fetch() calls to the Firebase REST API.

Users set a name on first visit (stored in localStorage). Multiple people can have the app open at once and see live updates via Firebase EventSource streaming.

---

## Tech stack
- **Single file:** Everything is in one `index.html` — HTML, CSS, and JavaScript all together
- **No frameworks:** Plain vanilla JavaScript only
- **Firebase:** Realtime Database via REST API (fetch calls, no Firebase SDK)
- **Hosting:** GitHub Pages
- **Charts:** Chart.js (loaded from CDN)
- **PWA:** Has a manifest so it can be installed on mobile as an app

---

## Data structure (what Firebase stores)
The entire app state is one JSON object saved to `tasktracker/state`:

```
state = {
  tabs: [],           // ordered list of tab names (strings)
  activeTab: null,    // currently selected tab name
  tasks: {            // tasks[tabName][bucketName] = array of task objects
    "Kyle": {
      "Active Projects": [ ...tasks ],
      "Shipped / HIT List": [ ...tasks ],
      ...
    },
    ...
  },
  timestamps: {},     // used for conflict resolution / merge logic
  tabCols: {},        // per-tab column visibility overrides
  tabBuckets: {},     // per-tab bucket overrides
  tabHistory: []      // log of tab-level events
}
```

### Task object fields
| Field | Description |
|-------|-------------|
| `id` | Unique random string |
| `customer` | Customer name (text) |
| `notes` | Details / Notes (textarea) |
| `owner` | Primary owner name (autocomplete) |
| `support` | Supporting person name (autocomplete) |
| `priority` | High / Medium / Low |
| `status` | See status options below |
| `due` | Due date (YYYY-MM-DD string) |
| `milestone` | Next Milestone (text) |
| `meetingNotes` | Meeting Notes (textarea) |
| `history` | Array of change log entries |
| `createdAt` | Timestamp |
| `completedAt` | Timestamp (when status = Complete) |

### Status options
`Ongoing, In Progress, In Process, Planned, Pending, Complete, On Hold, Delayed, Waiting on Response, Not Started, Idle, Scheduled, High Priority`

---

## Tabs and buckets
Default tabs: Kyle, Favs, Noah, Kendra, Software - Kendra, Service - Jonah, Jason, Design Bob & Cody

Each tab has its own set of buckets (collapsible sections):

| Tab | Buckets |
|-----|---------|
| Kyle | Active Projects, Shipped / HIT List, Sales Support, Working Groups, Machine Improvements, Misc |
| Favs | Active Projects, Shipped / HIT List, Sales Support, Machine Improvements |
| Noah | Active Projects, Shipped / HIT List, Sales Support, Machine Improvements |
| Kendra | Active Projects, Shipped / HIT List, Sales Support, Machine Improvements |
| Software - Kendra | Software Tasks |
| Service - Jonah | Active Service, Closed / Complete |
| Jason | Jason Tasks, Service Visibility |
| Design Bob & Cody | Bob O Tasks, Cody Tasks |

Tabs and buckets can be added/renamed/deleted in the UI. Users can also toggle column visibility per tab.

---

## Key features already built
- ✅ Tab navigation (PM tabs, Dashboard tab, My Tasks tab)
- ✅ Add / Edit / Delete tasks with full form
- ✅ Task history / change log per task
- ✅ Priority tags (High / Medium / Low) with color coding
- ✅ Due date badges with countdown ("Due in 3 days", overdue styling)
- ✅ Status pills with color coding
- ✅ Search and filter bar (search text, filter by owner/status/priority)
- ✅ Sort by any column
- ✅ Collapsible buckets
- ✅ Dashboard tab with charts (Chart.js) and summary table
- ✅ My Tasks view (filter by person's name across all tabs)
- ✅ Report generator (prints/exports a formatted report)
- ✅ CSV export (per tab and all tabs)
- ✅ Backup download and restore from JSON
- ✅ Real-time sync via Firebase EventSource stream
- ✅ Conflict resolution / merge logic for multi-user edits
- ✅ Update banner when remote data is newer
- ✅ User name prompt on first visit, shown in header
- ✅ Toast notifications
- ✅ Action sheet (mobile long-press menu for tasks)
- ✅ Tab configuration modal (rename, add/remove buckets, toggle columns, add custom columns)
- ✅ PWA installable on mobile

---

## Current version notes
- Version: v11.6
- Build date: 2026-05-07
- Firebase uses REST only (no SDK) — important to preserve this pattern
- Timestamps use `Date.now()` values; merge logic compares these for conflict resolution
- Local cache stored in localStorage under key `gi-tt-local-v3`
- Username stored in localStorage under key `gi-tt-username`

---

## What's broken / known issues
*(Update this section after each session)*
- None currently known

## What's next / planned
*(Update this section before each session to describe what you want to build)*
- [Describe your next feature here before starting a session]
