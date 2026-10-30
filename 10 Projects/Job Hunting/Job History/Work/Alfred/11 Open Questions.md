---
note_type: Research
topic: Alfred
created: 2026-10-26
updated: 2026-10-26
status: active
---
> Open questions about Alfred, each with a recommendation, the reason, and what to do next. Close a question by moving it to `10 Log/Progress` as a `decision` with a `why:`. AI: argue with these if the facts change.

## Summary

| # | Question | Recommendation |
| --- | --- | --- |
| 1 | Database engine | Local PostgreSQL 18 |
| 2 | Next steps | Foundation, Tasks, Today, in that order; keep the job-search minimum |
| 3 | Holidays | Reduce job-search volume in December, don't stop; build Alfred in the gap |
| 4 | AI inside Alfred | Same tools for everything; AI proposes, I accept |
| 5 | Cursor and Raycast | One MCP server plus a CLI for capture |
| 6 | Calendar and data grid | Library for the calendar's time grid; plain table first, grid library only if needed |
| 7 | UI/UX when I'm not a designer | Tokens, a few primitives, a screen brief, a screenshot loop |
| 8 | Existing systems (GTD and others) | Steal one rule at a time; Alfred configures systems, doesn't hard-code one |
| 9 | Systems to start with | Capture, next action, Today's Three, weekly review, log |
| 10 | Systems per task type | See [[12 Work Systems]] |
| 11 | Dashboard and quick answers | Spec is in the project plan; Today is slice 3 |

## 1. Database engine

**Recommendation: local PostgreSQL 18.**

- Already installed, auto-started, and backed up (see [[App Setup Plan]]).
- The budget data (53 accounts, 261 transactions) is already there.
- Several programs can use it at once: the app, an MCP server for Cursor and Raycast, a CLI. The MCP server works with Alfred closed.
- Dates, intervals, JSONB, and full-text search are built in. Shared logic goes in SQL views.

Costs: the app needs the Postgres service running; there is no single-file portability; mobile or sync later means more work.

Pick SQLite instead if you want Alfred to be one self-contained file and nothing else to run. Don't put a live database file in Dropbox either way. Back it up there.

**Next:** decide, then create `alfred` and `alfred_dev`.

## 2. Next steps

**Recommendation: build the smallest loop first: tasks in, Today out.**

This week:

- [ ] Decide the engine (15 min).
- [ ] Create `alfred` and `alfred_dev` databases and migration `001_core.sql` (tasks, log_entries, time_entries).
- [ ] Replace the stub `App.svelte` with the zone shell and light tokens.
- [ ] Import `08 Next Steps` and `10 Log/Progress` with a one-off script.
- [ ] Show the Today view with one real query.
- [ ] Keep the job-search minimum going (see question 3).

Rules:

- Slice appetite is two weeks. Over budget means cut scope, not extend.
- Don't start Budget, Job Hunt, Calendar, or the grid until Today has been in daily use for a week.
- Log each finished slice in `10 Log/Progress`.

## 3. The holidays

**Recommendation: lower volume, not zero. Spend the saved effort on things that pay off in January.**

Dates: Thanksgiving is Thursday Nov 26, Christmas is Friday Dec 25, and New Year's Day is Friday Jan 1. Hiring commonly slows from mid-December to early January and picks up after. Check this against your own response rates once Alfred tracks them.

Why not stop: you are about $1,000 a month short, and `09 Roadmap` says money comes before everything else. Why not push harder: low response in the dip, and it burns the energy you need for interviews in January.

| Phase | Dates | Job search | Alfred | Michael |
| --- | --- | --- | --- | --- |
| Full minimum | Now to Nov 22 | Action Plan as written: weeknights 30 min, Sat 2 h, Sun 1 h | Foundation, Tasks, start Today | Ask for ERPNext status |
| Reduced | Nov 23 to Dec 18 | Three job-board sweeps a week, Saturday interview practice, one follow-up batch on Sunday | Today and Log in daily use | A holiday message before Thanksgiving: share one ERPNext finding, ask where the CEO decision stands |
| Low gear | Dec 19 to Jan 3 | Draft applications, don't send. Practice interviews out loud and record them. Fix resume and LinkedIn to lead with React and AWS serverless. | AI access, then Budget | Skip, or one short note |
| Ramp | Jan 4 on | Send the drafted applications in the first week back. Back to the full minimum. | Job Hunt module, so the pipeline is tracked | Monthly check-in |

Guardrails against tool churn:

- The job-search minimum (about 5.5 hours a week) never gets borrowed for Alfred. Alfred time comes from weekday mornings and days off.
- If the minimum is missed two weeks in a row, pause Alfred until it is back.
- Interview practice and the five stories are not optional in the slow weeks; they are the point of the slow weeks.
- The 3-month milestones due Jan 24 (three close-fit interviews, five rehearsed stories, resume and LinkedIn updated) are the scoreboard.

Suggested week, adjust to energy:

| When | Use |
| --- | --- |
| Mon, Wed, Fri 5 to 6:30 am | Alfred build |
| Tue, Thu 5 to 6 am | Interview practice or Michael |
| Weeknights, 30 min | Job-board sweep and one targeted application |
| Saturday, 2 h | Interview prep, one story |
| Sunday, 1 h | Follow-ups, weekly review |

## 4. How AI fits into Alfred

**Recommendation: build one set of named tools and let everything use it.**

The same commands feed the menu, the palette, the MCP server, the CLI, and the in-app AI. That keeps AI honest: it can only do what a keyboard shortcut can do.

Layers, in build order:

| Layer | What | When |
| --- | --- | --- |
| 1 | Read-only SQL role through postgres-mcp (Raycast already has it) | As soon as the data is in Postgres |
| 2 | Alfred MCP server with named tools | Slice 5 |
| 3 | Markdown exports (`today.md`, `week.md`, `status.md`) for Claude Projects and Obsidian | Slice 5 |
| 4 | In-app AI: Ask mode, daily brief, capture triage, job-fit screener, weekly review | After slice 5 |

In-app uses worth building, in value order:

1. **Ask:** "What did I do this week?", "history of the power bill", answered from saved views, with AI for the variations.
2. **Brain dump to tasks:** paste or dictate a rambling note, get proposed tasks with energy, time, and context filled in. I accept or edit.
3. **Weekly review:** an AI-written summary of the week from the log, plus stuck items and goals that got no attention.
4. **Job-fit screener:** the existing `evaluate-job-fit` prompt, returning JSON saved on the lead.
5. **Daily brief:** the Today screen as a short paragraph, optional.

Rules:

- AI proposes; I accept. Writes show what will change.
- AI never gets write SQL. It calls named tools only.
- AI writes are logged with `source: ai`.
- API key lives in the macOS Keychain, not a file in the repo.
- No embeddings or vector database yet. Full-text search plus SQL covers a vault this size. Revisit when a real question fails.

## 5. Cursor and Raycast

**Recommendation: one MCP server, one CLI, and an updated vault rule.**

**Cursor**

- Add the Alfred MCP server in Cursor's MCP settings. Cursor can then read tasks and log work while coding.
- Update `.cursor/rules/vault.mdc` (done) so it says records live in Alfred and the vault holds documents.
- In the app repo, `AGENTS.md` should point at the migrations folder as the schema truth.
- Optional: a `repo_path` on tasks so a task can name the project to open.

**Raycast**

- Raycast installs MCP servers and exposes them as @-mentions, so the same Alfred server works there with no extension.
- Quick capture: a script command or quicklink that runs `alfred add "..."` (writes straight to the database, works with the app closed) or opens `alfred://capture?text=...` (a deep link, needs the app).
- A real Raycast extension (React and TypeScript) is a later step, only if MCP plus capture don't cover Today and the timer.

**Order:** CLI and MCP read tools first. They are cheap and make everything else more useful.

## 6. Calendar and data grid

**Recommendation: use a library for the calendar's time grid; start the data grid as a plain table.** Neither is a bloat risk for a local Tauri app. The cost is lock-in and styling, not download size.

Found in current docs (verify release dates and issues before committing; I haven't tested either):

| Need | Option | Notes |
| --- | --- | --- |
| Calendar | SVAR Svelte Calendar (`@svar-ui/svelte-calendar`) | Svelte 5, MIT free edition with day, week, and month views, drag to create, move, and resize, iCal import and export. Agenda and year views are paid. |
| Grid | SvGrid (`@svgrid/grid`) | Svelte 5 native, MIT core: virtualization, inline editing, cell-range selection, copy and paste as TSV, fill handle. Spreadsheet formulas and xlsx import are in a paid package. Newer library, so check maturity. |
| Grid | SVAR Svelte DataGrid | Svelte 5 compatible: sorting, filtering, in-cell editing, virtual scrolling. |

**Calendar**
- Month grid: easy to build yourself. Week and day views with overlapping events, drag-to-create, and resize are where the time goes. Use a library.
- Wrap it in one adapter file that maps `core.events` to the component's shape.
- What you need first is not a calendar. It is an Upcoming list on Today. Build that first.
- Later: read Apple Calendar through EventKit instead of building sync.

**Data grid**
- The budget needs sort, filter, search, and editing a category. Postgres does the sorting and filtering. A plain `<table>` handles hundreds to a few thousand rows.
- Add a grid library when you want spreadsheet behavior: range select, paste, fill, undo. Never write that yourself.
- You don't need Excel inside Alfred. For formulas, export CSV and open it in Numbers or Sheets. Alfred is the database; the spreadsheet is a view.

**Test before adding any library**
1. Name today's problem it fixes, in one sentence.
2. Wrap it behind one adapter file.
3. Svelte 5 native, not a wrapper around another framework.
4. MIT, with nothing you need locked behind a paid tier.
5. Released in the last six months.
6. It accepts your CSS variables, so light and dark both work.

## 7. UI/UX when I'm not a designer

**Recommendation: constrain the AI instead of asking it to design.**

1. **Pick references.** Two or three apps whose feel you want (Things, Linear, Obsidian, Zed). Save screenshots in the repo and give them to the AI.
2. **Build a tiny design system first.** Tokens plus eight primitives (Button, Input, ListRow, Card, Panel, Kbd, EmptyState, CommandPalette). Screens are assembled from these only.
3. **Write the rules where the AI reads them.** In the app's `AGENTS.md`: no new colors; spacing scale 4, 8, 12, 16, 24; type sizes 12, 13, 14, 20; every action reachable by keyboard; every view has an empty state; one primary action per screen.
4. **Write a screen brief before asking for code.** Five lines: what I'm trying to do, what's on screen, the one primary action, the keys, and the empty and error states.
5. **Close the loop with screenshots.** Run the app, take a screenshot, ask the AI to critique it against the rules, fix, repeat. Two or three rounds.
6. **Judge by using it.** Use a screen for two days before polishing it.
7. **Use an accessible headless library** for dialogs, popovers, comboboxes, and context menus. Focus and keyboard handling are easy to get wrong by hand.

Flow rules that matter most:

- Capture in under two seconds from anywhere (global hotkey).
- Show the next action, not a list of everything.
- Undo instead of confirm.
- No modal chains.
- Remember the last energy and context.
- Calm by default: warm off-white light theme, no flashing or bouncing motion, a zoom control, readable base size.

## 8. Finding systems that already exist

**Recommendation: don't adopt a system. Steal one rule at a time, and make Alfred configurable enough to hold them.**

How to find them:
- Search by the problem ("system for a weekly review", "pipeline stages for a job search"), not by the brand.
- Read the primary source or a good summary, not ten blog posts.
- Ask an AI: "Name three established systems for X. What does each assume about the user? Which survives a tired, headache day?" Then pick one rule from the winner.

How Alfred holds a system: every system is four things. **Entities** (what you track), **states** (the stages), **a ritual** (the recurring habit), and **a view** (where you see it). In Alfred that is: a status list, a few fields, a recurring task with a checklist, and a Dashboard card. Config over core tables, not code per system.

The catalog is in [[12 Work Systems]].

## 9. Systems to start with

**Recommendation: five, and nothing else for the first month.**

1. **Capture:** one inbox, reachable in two seconds.
2. **Next action:** every task is a concrete next step with energy, time, and context (you already have this).
3. **Today's Three:** at most three tasks on Today.
4. **Weekly review:** 15 minutes on Sunday.
5. **Log:** one line when something gets done, decided, or stuck.

Add the job pipeline when Job Hunt ships and the cash-flow calendar when Budget ships. Learning, projects and areas, and work-in-progress limits wait.

## 10. Systems per task type

See [[12 Work Systems]]: Programming, Job Hunt, Budget, Learning, Interview practice, Life admin and health.

## 11. Dashboard and quick answers

See the **Dashboard: Today** and **Quick answers** sections in [[10 Clients/Work/BWS/Projects/Alfred/Notes|Alfred project plan]]. Today is slice 3. The quick answers (power bill history, this week's summary, this week's goals) are saved SQL views, available to the palette, MCP, and AI.
