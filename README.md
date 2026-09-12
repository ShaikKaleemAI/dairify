# Dairify — Own Your 24 Hours

![status](https://img.shields.io/badge/status-active-brightgreen)
![stack](https://img.shields.io/badge/stack-vanilla%20JS-yellow)
![deps](https://img.shields.io/badge/dependencies-zero-informational)
![privacy](https://img.shields.io/badge/data-local%20only-lightgrey)
![license](https://img.shields.io/badge/license-MIT-blue)

A 24-hour activity tracker that treats your day as a timeline, not a list.
Log what you did, when, and for how long; Dairify turns it into a visual
timeline, a donut breakdown, and running stats — with an optional PIN lock
so the day stays private.

Single HTML file. No framework, no backend, no accounts — everything lives
in `localStorage`.

## Why this project is on my resume

- **A real timeline data model, not a to-do list wearing a clock icon.**
  Activities are stored as `{start, end}` minute ranges per date, with
  overlap detection (`hasOverlap`) and a **smart-split** add flow
  (`smartSplitAdd`) that automatically breaks a new entry around anything
  it collides with, instead of silently overwriting time you already
  logged.
- **A lock screen that behaves like a real app lock**, not a toy password
  field: it distinguishes "app freshly opened" from "returning after the
  screen was locked" (`needsLockOnResume`), so you're not re-prompted
  during continuous use, and passwords are hashed (`hashPwd`) before being
  compared or stored — not kept in plain text.
- **Every visual reads from one source of truth.** The timeline, the donut
  chart, the stat counters, and the exported summary are all derived from
  `getActivities(date)` — there's no second copy of "today's data" that
  can drift out of sync.

## Features

- **Visual timeline** — the day rendered as a 24-hour strip, activities
  laid out proportionally to their duration, with a live "now" marker.
- **Smart quick-add** — type a name and a start/end time; overlapping
  ranges are auto-split around existing entries instead of rejected or
  silently merged.
- **Custom analog clock time picker** with carry-forward time pre-fill
  (a new entry defaults to starting where the last one ended) and activity
  name autocomplete from your own history.
- **Stats & donut chart** — total tracked time and a per-activity
  breakdown, hand-drawn as SVG (no charting library).
- **Day navigation** — jump between dates, jump to today, with a labeled
  date list in the sidebar.
- **Edit / delete / clear** — inline edit modal, quick-delete, and a
  confirm-gated "clear day" action.
- **Export** — copies a formatted plain-text summary of the day
  (activity list + total tracked time) straight to the clipboard.
- **App lock** — optional PIN/password, hashed before storage, asked only
  when the app is opened fresh or resumed after being backgrounded — not
  on every interaction.
- **Settings** — 12-hour/24-hour time format toggle, dark/light theme
  (persisted), password management.
- **Keyboard shortcuts panel**, live clock, midnight auto-refresh (the
  view rolls over to the new day on its own), and a full toast
  notification system for feedback.
- **Installable feel** — ambient background orbs, smooth scroll-to-current-
  hour on load, and themeable UI built from one design-token system.

## Architecture

```
Dairify-Enhanced.html
├── <style>          design tokens, ambient background, timeline + modal styles
└── <script>
    ├── Data layer      loadDB/saveDB, getActivities(date), hasOverlap,
    │                   smartSplitAdd/addToDate — the timeline model
    ├── Rendering       renderTimeline, renderActivityList, renderStats,
    │                   renderDonut (raw SVG) — all read-only against the DB
    ├── Navigation       buildDateList, switchDate, shiftDay, jumpToday,
    │                   scheduleMidnightRefresh
    ├── App lock         hashPwd, isPasswordSet, needsLockOnResume,
    │                   markUnlocked/markNeedsLock, showLock/hideLock
    ├── Settings          setFormat (12h/24h), setTheme/toggleTheme,
    │                   saveNewPassword
    └── UX chrome          toasts, modals, keyboard shortcuts, live clock,
                          smooth-scroll helpers
```

## Run it

Open the file directly in a browser — no build step, no server required.
For clipboard export to work reliably, serve it over `http://` or
`https://` rather than `file://`:

```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

## Honest limitations

- All data is local to the browser it's used in — there's no account, no
  sync, and no server-side backup. Clearing site data clears your history.
- The app lock protects against casual glances at the screen, not against
  someone with access to the browser's local storage directly — it's a
  privacy screen, not encryption at rest.
