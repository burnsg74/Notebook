---
note_type: Log
topic: Progress
created: 2026-10-24 07:45
format: v1
---
> Record of what got done, decided, and learned. One line per entry, newest at the bottom. This is the evidence of progress; the AI and Alfred read it for weekly reviews and to show how far I've come. When Alfred's Log module ships, this note is imported once and frozen.

## How to log

No schedule. Add a line when something gets done, decided, or noticed. Small things count.

```
- 2026-10-24 07:45 | done | Message one former colleague | goal: work | time: 15m | link: 08 Next Steps
```

Each line: date and optional time, then a type, then a short description, then optional `key: value` fields.

| Type | Use for |
| --- | --- |
| `done` | Finished a task (from `08 Next Steps` or not) |
| `milestone` | A milestone in `09 Roadmap` became true |
| `decision` | A choice that changes the plan, with the reason in a `why:` field |
| `win` | Something good that wasn't a task (a reply, a good day, a compliment) |
| `stuck` | Something blocked, so the weekly review can deal with it |
| `time` | Time spent on a task, usually written by Alfred's timer (`duration:` and `id:` fields) |

| Field | Values |
| --- | --- |
| `goal` | Same values as `08 Next Steps` |
| `time` | Time spent, if worth knowing |
| `why` | Reason, mainly for decisions |
| `link` | Note to open |
| `duration` | For `time` lines: `25m`, `1h10m` |
| `id` | Task ID from `08 Next Steps`, when the line is about a task |

## Asking the AI

Paste the last week or month of entries with `05 Current Status` and `09 Roadmap`, then ask:

- "Summarize what I got done this week, grouped by goal."
- "Which goals got no attention this month?"
- "What's been stuck more than once?"

## Entries

- 2026-10-24 | done | Built the Action Plan notes: map, goals, about me, work history, health, health log, current status, systems and tools, tool ideas, next steps, roadmap | goal: systems | link: 00 Start Here
- 2026-10-24 | decision | Health stays in its own note, with an unscheduled health log | goal: health | why: keep health out of unrelated chats; logging fits around other tasks, not a fixed time
- 2026-10-24 | decision | Dropped Ensemble Weather AI job lead | goal: work | why: looked like a scam
- 2026-10-24 | decision | PM app and Master app are the same app; name to be decided | goal: apps | link: 07 Tool Ideas
- 2026-10-24 | decision | The Action Plan notes are the data for my apps; no second database | goal: apps | link: 07 Tool Ideas
- 2026-10-24 | done | Rewrote AI Instructions: AI rules, model per question, setup, tips for better answers | goal: systems | link: AI Instructions
- 2026-10-24 | decision | Moved the Action Plan to 10 Clients/Work/Alfred and pointed AGENTS.md and the Cursor vault rule at 00 Start Here | goal: systems | why: it's a live system and app data, not research; the rules pointed at a note that didn't exist
- 2026-10-24 | done | Prefilled About Me and Goals from job-hunt notes (schedule, household, money floor, target role) | goal: systems | link: 01 About Me
- 2026-10-24 | done | Rewrote the PM app plan as the Master app, reading and writing these notes instead of SQLite | goal: apps | link: 07 Tool Ideas
- 2026-10-25 | decision | Merged CareerOS, Job Hunt, and Job Hunt 2 into Alfred/Job Hunt; deleted the duplicate copies and combined both Skill gaps lists | goal: work | why: Job Hunt 2 and CareerOS were copies of the same notes, so three folders meant three places to check | link: 00 Start Here
- 2026-10-26 | decision | The app is named Alfred. PM app, Master app, and Personal System are retired names; notes and folders renamed | goal: apps | why: one name for the app, the notes, and the repo | link: 07 Tool Ideas
- 2026-10-26 | decision | A database is the source of truth for records (tasks, log, budget, job leads, events). Markdown holds documents | goal: apps | why: records need queries and history (power bill, weekly summary); documents need to stay prose. Supersedes the 2026-10-24 decision to use notes as the data | link: 07 Tool Ideas
- 2026-10-26 | decision | Budget and Job Hunt merge into Alfred as modules; one app, not several | goal: apps | why: one place to look; separate apps meant separate places to forget | link: 07 Tool Ideas
- 2026-10-26 | decision | Light theme first; dark theme later via a second token set | goal: apps | why: ship one theme well; colors live only in CSS variables so dark is a swap | link: 07 Tool Ideas
- 2026-10-26 | done | Wrote 11 Open Questions and 12 Work Systems; rewrote the Alfred project plan around the database | goal: apps | link: 11 Open Questions
- 2026-10-29 | done | Updated Inbox local web app notes so the datasource is vault markdown/CSV instead of Postgres | goal: apps | link: 00 Inbox/Local web app setup
- 2026-10-29 | done | Rewrote Local web app setup for multiple apps: named *.localhost URLs, auto-start, backend only when needed | goal: apps | link: 20 Notes/Local web app setup
- 2026-10-29 | decision | Local web apps use {app}.localhost via nginx, not 127.0.0.1:port | goal: apps | why: named URLs are easier day to day; ports stay behind nginx for APIs only | link: 20 Notes/Local web app setup
- 2026-10-29 | decision | Local web app APIs use node:http, not Fastify/Express, unless the API grows enough to need a framework | goal: apps | why: four file endpoints do not justify a framework | link: 20 Notes/Local web app setup
- 2026-10-29 | decision | Local web frontends default to Vite + Svelte SPA, not SvelteKit | goal: apps | why: static SPA needs no Kit; matches Alfred's frontend choice | link: 20 Notes/Local web app setup
- 2026-10-29 | decision | Combined Learning, How To, Research, Services Tools, and Reference into `20 Notes`; type is `note_type` in frontmatter, not folder | goal: systems | why: one place to look; folders duplicated what YAML already said | link: README
- 2026-10-29 | done | Moved those folders into `20 Notes`, merged Obsidian/Warp/Zed service duplicates into the tool notes, updated README and AGENTS | goal: systems | link: 20 Notes
