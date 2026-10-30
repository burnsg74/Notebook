---
note_type: Research
topic: Alfred
created: 2026-10-24 07:25
updated: 2026-10-26
status: active
---
> What Alfred is, what it replaces, what goes in it, and in what order to build it. AI: help me keep scope small; push back when a module grows before the previous one is in daily use.

## Decisions

| Date | Decision | Why |
| --- | --- | --- |
| 2026-10-26 | **The app is named Alfred.** PM app, Master app, and "Personal System" are old names for the same thing. | One name for the app, the notes, and the repo. |
| 2026-10-26 | **One app.** Budget and Job Hunt merge into Alfred as modules. Freelance joins later. | One place to look is the whole point. Separate apps meant separate places to forget. |
| 2026-10-26 | **A database is the source of truth for records** (tasks, log, budget, job leads, events, milestones). **Markdown holds documents** (About Me, Goals, Health background, Work History, prompts, how-tos). | Records need queries ("history of the power bill", "what I did this week"), joins, and dates. Documents need to be read and edited as prose. |
| 2026-10-26 | **Light theme first.** Dark comes later, as a second set of tokens. | Ship one theme well. Colors live only in CSS variables, so dark is a swap, not a rewrite. |

Still open: which database engine (recommendation: local PostgreSQL 18). See [[11 Open Questions]].

## Rules for building

- Build only what removes a real friction from `06 Systems and Tools` or moves a goal in `02 Goals`.
- Smallest useful version first, used for two weeks before the next module starts.
- One module in progress at a time.
- Local-first, on my Mac. No account, no hosted service.
- Records live in the database. Anything else (exports, caches, search indexes) must be rebuildable from the database or the notes.
- A module is not done until it has: a migration, a Today/Dashboard card, and a read tool the AI can call. AI access is part of the module, not a later project.
- Until a module ships, keep using the markdown notes by hand. Import once on the day it ships, then freeze the old file. Never run both.

## Modules

| Module | Replaces | Smallest useful version | State |
| --- | --- | --- | --- |
| **Today** (dashboard) | Opening five apps to decide what to do | Three tasks to do now, running timer, what got done yesterday, what is due soon | Not started |
| **Tasks** | `08 Next Steps` | List, add, complete, filter by energy/context/time | Not started |
| **Log** | `10 Log/Progress` (health log later) | One-line entries: done, decision, win, stuck; weekly summary | Not started |
| **Budget** | The local `budget` database and Next.js app (53 accounts, 261 transactions) | Account and bill history, upcoming bills, "will I go negative this month" | Data exists, no Alfred UI |
| **Job Hunt** | `Alfred/Job Hunt` working files and the CareerOS prompts | Paste a posting, get the fit result, save a lead with status and next action | Prompts exist, no UI |
| **Calendar** | Nothing yet | Agenda list of the next 7 days; month/week view later | Not started |
| **Notes** | Opening Obsidian to find context | Read-only search over the vault | Not started |
| **Freelance** | A note per lead | Wait for the first paying client | Parked |

Modules and tables are detailed in [[10 Clients/Work/BWS/Projects/Alfred/Notes|Alfred project plan]].

## Suggested build order

To confirm or change.

1. **Foundation:** shell, light tokens, database, migrations, command palette. The app is currently a stub.
2. **Tasks,** with a one-time import of `08 Next Steps`.
3. **Today,** the dashboard. This is the first screen I open every day.
4. **Log and current-task timer,** with a one-time import of `10 Log/Progress`.
5. **AI access:** read-only MCP server and markdown exports, so Cursor, Raycast, and Claude can read Alfred.
6. **Budget,** starting read-only (history, upcoming bills, cash-flow low point).
7. **Job Hunt,** starting with the pipeline and the fit screener.
8. **Calendar and data grid,** only when a module needs them.

## Ideas parking lot

New ideas go here first, with one line on the problem. They don't get built until they move into the list above.

- Health log inside Alfred (today it is `10 Log/Health`).
- Menu bar timer and quick capture.
- In-app weekly review, with an AI-written summary.
