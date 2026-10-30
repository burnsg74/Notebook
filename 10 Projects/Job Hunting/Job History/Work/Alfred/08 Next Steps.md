---
note_type: Research
topic: Alfred
created: 2026-10-24 07:35
updated: 2026-10-24
format: v1
---
> Menu of tasks to pick from, tagged by type, energy, time, and context. Pick one that fits right now; no need to work top to bottom. AI and Alfred: suggest from here, and log finished tasks to `10 Log/Progress`. When Alfred's Tasks module ships, this note is imported once and frozen.

## How to pick

1. **Energy:** how do I feel? Low, medium, or high (scale in `01 About Me`).
2. **Context:** where am I? (`06 Systems and Tools`, Contexts)
3. **Time:** how long do I have?
4. Pick one task that matches all three. Prefer tasks tied to this week's one priority in `05 Current Status`.

On a bad day, one low-energy task counts as a win.

## Task format

One task per line, a checkbox, the title, then `key: value` fields split by `|`:

```
- [ ] Message one former colleague | type: do | energy: med | time: 15m | context: laptop | goal: work | added: 2026-10-24
```

| Field | Values |
| --- | --- |
| `type` | `learn` (understand something), `shortcut` (practice until it's habit), `do` (one-off, just do it), `recurring` (repeats). _Starting set; change as the types get decided._ |
| `energy` | `low`, `med`, `high` |
| `time` | Rough minutes or hours: `5m`, `15m`, `30m`, `1h`, `2h` |
| `context` | `audio`, `tablet`, `laptop`, `focus` (from Contexts) |
| `goal` | `life`, `money`, `health`, `work`, `systems`, `tools`, `apps` (from `02 Goals`) |
| `every` | Recurring only: `day`, `week`, `month`, or a weekday like `sat` |
| `added` | Date added |
| `link` | Optional: note to open |
| `status` | Optional: `doing` while in progress. No field means not started. |
| `note` | Optional: one-line description |
| `id` | Added by Alfred on import; don't edit |

When a task is done, check it and add a line to `10 Log/Progress`. Recurring tasks stay unchecked; just log each time.

## Do

- [ ] Fill in the money lines in 05 Current Status (pay, take-home, gap) | type: do | energy: low | time: 15m | context: laptop | goal: money | added: 2026-10-24 | link: 05 Current Status
- [ ] Fill in priority ranking in 02 Goals | type: do | energy: med | time: 15m | context: laptop | goal: life | added: 2026-10-24 | link: 02 Goals
- [ ] Fill in headaches and current meds in 04 Health | type: do | energy: low | time: 15m | context: tablet | goal: health | added: 2026-10-24 | link: 04 Health
- [ ] Add first entry to the health log | type: do | energy: low | time: 5m | context: tablet | goal: health | added: 2026-10-24 | link: 10 Log/Health
- [ ] Review ERPNext customization options for Michael | type: do | energy: high | time: 1h | context: focus | goal: money | added: 2026-10-24 | link: ERPNext
- [ ] Message one former colleague for a 15-minute catch-up | type: do | energy: med | time: 15m | context: laptop | goal: work | added: 2026-10-24 | link: 4 - Action Plan
- [ ] Find two close-fit roles on Wellfound or Work at a Startup | type: do | energy: med | time: 30m | context: laptop | goal: work | added: 2026-10-24 | link: 1 - Wellfound
- [ ] Get a professional headshot | type: do | energy: med | time: 30m | context: laptop | goal: work | added: 2026-10-24 | link: Professional headshot
- [ ] Set up a simple budget spreadsheet (cap 2 hours) | type: do | energy: med | time: 2h | context: laptop | goal: money | added: 2026-10-24
- [x] Pick a name for Alfred | type: do | energy: low | time: 15m | context: tablet | goal: apps | added: 2026-10-24 | link: 07 Tool Ideas
- [x] Decide: one app or several | type: do | energy: med | time: 30m | context: laptop | goal: apps | added: 2026-10-24 | link: 07 Tool Ideas
- [ ] Decide the Alfred database engine (Postgres recommended) | type: do | energy: low | time: 15m | context: laptop | goal: apps | added: 2026-10-26 | link: 11 Open Questions
- [ ] Create the alfred and alfred_dev databases and migration 001 (tasks, log_entries, time_entries) | type: do | energy: high | time: 1h | context: focus | goal: apps | added: 2026-10-26 | link: Notes
- [ ] Replace the stub App.svelte with the zone shell and light tokens | type: do | energy: high | time: 2h | context: focus | goal: apps | added: 2026-10-26 | link: Notes
- [ ] Message Michael before Thanksgiving: share one ERPNext finding, ask where the CEO decision stands | type: do | energy: low | time: 15m | context: laptop | goal: work | added: 2026-10-26 | link: Michael Mussulis | note: send by Nov 25

## Learn

- [ ] Rehearse the Makpar portal story out loud (problem, what I did, result) | type: learn | energy: med | time: 30m | context: focus | goal: work | added: 2026-10-24 | link: 03 Work History
- [ ] Listen to one episode on interviewing or AI tools | type: learn | energy: low | time: 30m | context: audio | goal: work | added: 2026-10-24
- [ ] _fill in (Cursor agent workflow, Svelte 5, Tauri, ...)_ | type: learn | energy: high | time: 1h | context: focus | goal: tools | added: 2026-10-24
- [ ] Pick one rule from GTD and one from Shape Up to try for two weeks | type: learn | energy: low | time: 30m | context: tablet | goal: systems | added: 2026-10-26 | link: 12 Work Systems

## Shortcuts

- [ ] _fill in (for example: Raycast quick capture, Cursor agent panel, Obsidian quick switcher)_ | type: shortcut | energy: low | time: 5m | context: laptop | goal: tools | added: 2026-10-24

## Recurring

- [ ] Job board check and 1–2 targeted applications | type: recurring | every: day | energy: low | time: 30m | context: laptop | goal: work | added: 2026-10-24
- [ ] Interview prep: one story | type: recurring | every: sat | energy: med | time: 2h | context: focus | goal: work | added: 2026-10-24
- [ ] Follow-ups and 1–2 network messages | type: recurring | every: sun | energy: med | time: 1h | context: laptop | goal: work | added: 2026-10-24
- [ ] Update 05 Current Status and pick next week's priority | type: recurring | every: week | energy: low | time: 15m | context: laptop | goal: systems | added: 2026-10-24 | link: 05 Current Status
- [ ] Check in with Michael (share a win, not "any update?") | type: recurring | every: month | energy: low | time: 15m | context: laptop | goal: work | added: 2026-10-24
