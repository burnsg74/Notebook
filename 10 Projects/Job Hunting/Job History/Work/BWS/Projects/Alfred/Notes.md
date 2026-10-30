---
note_type: Project
created: 2026-09-28 17:38
updated: 2026-10-26
---
# Alfred

A personal digital assistant for one person, on the Mac: it tells me what to do now, shows what I have done and what is coming, and answers questions about my own records (budget, job leads, tasks, log). It replaces the PM app, the Master app, the Budget app, and the Job Hunt app. Earlier planning used those names; they are all Alfred now.

The experience should feel like Zed or Obsidian: a persistent shell, keyboard-first, fast, quiet. Not a website in a window.

Goals, decisions, and module order are in [[07 Tool Ideas]]. Open questions with recommendations are in [[11 Open Questions]]. Systems Alfred encodes (GTD, pipelines, reviews) are in [[12 Work Systems]].

- **App:** `/Users/greg/Code/Local Apps/alfred` (Tauri 2, Svelte 5, Vite, plain JavaScript, no SvelteKit, no TypeScript)
- **Architecture notes:** the zone-based shell, command registry, tray, and global hotkey design from 2026-10-25 still applies. Only the data layer changed.

> Build one slice at a time. Leave the app usable at the end of each slice before starting the next.

## Principles

- **Records in a database, documents in markdown.** See [Data](#data).
- **Local-first.** Core features work offline, with no account.
- **Focus before features.** The first screen answers "what do I do now?" Everything else supports that.
- **Keyboard-first.** Every common action has a key and a command-palette entry.
- **One command registry.** Menu, palette, tray, MCP tools, and the in-app AI all call the same commands.
- **AI is built in, not bolted on.** Every module ships with a read tool for AI. AI proposes; I accept.
- **Low-energy friendly.** Works on a headache day: few choices, no animation that demands attention, readable type, zoom control.
- **Interoperable.** Cursor, Raycast, Obsidian, and Claude read Alfred through defined doors (MCP, CLI, exports), not by poking at tables.

## Decisions

| Decision | Detail |
| --- | --- |
| Shell | Tauri 2 + Svelte 5 SPA on Vite. Plain JS. No SvelteKit, no TypeScript. |
| Source of truth | Database for records. Markdown for documents. |
| Engine | Recommended: local PostgreSQL 18 (already installed and backed up; budget data is already there). Alternative: SQLite. Confirm in [[11 Open Questions]]. |
| Database access | Rust owns the connection. The frontend calls commands, never SQL. |
| Shared logic | SQL views and functions (`v_today`, `v_week_summary`, `v_account_history`) so the app, MCP server, CLI, and `psql` all give the same answers. |
| Theme | Light first, using Catppuccin Latte values behind the same token names as Mocha, so dark is a second set later. |
| Cache | Module-level `.svelte.js` stores with stale-while-revalidate. Rust emits a `data:changed` event when a write lands (from the app, the CLI, or the MCP server). No disk cache. |

## Data

### Records and documents

| | Records | Documents |
| --- | --- | --- |
| Examples | Tasks, log entries, transactions, accounts, job leads, events, milestones | About Me, Goals, Health background, Work History, prompts, how-tos, interview prep |
| Lives in | Database | Markdown in the Work vault |
| Edited in | Alfred (also CLI and AI tools) | Obsidian or Cursor |
| Alfred's access | Read and write | Read-only index (title, path, text) for search and AI context |

The old `format: v1` notes (`08 Next Steps`, `10 Log/Progress`, `10 Log/Health`, `09 Roadmap`) were records stored as text. They become import sources. Each one is imported once, then gets a banner: `Frozen: imported into Alfred on YYYY-MM-DD`.

### Cutover rule

Never run two sources of truth. Until Tasks ships, keep using `08 Next Steps` by hand. On ship day: import, freeze the note, switch. Same for Log, Budget, and Job Hunt.

### Schemas

One database (`alfred`), one Postgres schema per module.

| Schema.table | Key columns | Notes |
| --- | --- | --- |
| `core.tasks` | `id`, `title`, `status` (todo, doing, done), `kind` (do, learn, shortcut, recurring), `energy`, `time_min`, `context`, `goal`, `project_id`, `due_on`, `scheduled_at`, `every`, `note`, `link`, `position`, `created_at`, `done_at` | Maps 1:1 from the old task line fields. |
| `core.time_entries` | `task_id`, `started_at`, `ended_at` | Totals per task and per week come from here. |
| `core.log_entries` | `id`, `at`, `kind` (done, milestone, decision, win, stuck), `body`, `goal`, `why`, `task_id`, `source` (me, ai, import) | Replaces `10 Log/Progress`. `source` marks AI-written rows. |
| `core.goals`, `core.milestones` | `goal`, `text`, `by`, `done_at` | From `09 Roadmap`. |
| `core.projects` | `id`, `name`, `area`, `status` | Outcome-based work. Tasks may belong to one. |
| `core.events` | `id`, `title`, `starts_at`, `ends_at`, `all_day`, `rrule`, `source` | Calendar. Source can later be Apple Calendar (read-only). |
| `core.documents` | `path`, `title`, `mtime`, `body` (full-text indexed) | Read-only mirror of vault notes. Rebuilt from disk. |
| `budget.accounts` | existing: name, type, institution, `expected_amount_cents`, `due_day`, `schedule_active`, `archived` | Moved from the existing `budget` database. |
| `budget.transactions` | existing: `account_id`, `posted_on`, `description`, `amount_cents`, `classification`, `fingerprint` | Same. Keep the fingerprint dedupe. |
| `budget.import_batches` | existing | Same. |
| `jobs.leads` | `id`, `company`, `title`, `url`, `source`, `posted_at`, `status`, `fit` (JSONB from the fit screener), `next_action`, `next_action_on`, `applied_at`, `note` | Pipeline. |
| `health.entries` | `at`, `kind`, `value`, `note` | Later. Replaces `10 Log/Health`. |

Rules: money is integer cents. Timestamps are `timestamptz`. Every table has `created_at`. Migrations are numbered SQL files in the repo, applied in order, never edited after they run.

Backups: nightly `pg_dump` per [[App Setup Plan]], written to a Dropbox folder so it leaves the machine.

### Moving the existing budget data

1. `pg_dump` the `budget` database. Restore into the `alfred` database under schema `budget`.
2. Point Alfred's Budget module at it. Leave the Next.js budget app running until Alfred can import a statement and show account history.
3. Retire the Next.js app only after that parity check.

## Dashboard: Today

The first screen. Its job is to reduce stress: one clear next move, evidence of progress, and no surprises.

| Zone | Shows | Source |
| --- | --- | --- |
| **Now** | One recommended task, plus two alternates, matched to current energy, context, and time. A running timer if one is active. | `v_today` (tasks by fit; this week's priority first) |
| **This week** | The one priority. Goal progress: tasks done, time spent, per goal. | `core.goals`, `core.time_entries`, `core.log_entries` |
| **Done** | Yesterday and this week so far, one line each, grouped by goal. | `core.log_entries` |
| **Upcoming** | Next 7 days: events, due tasks, bills due (from `due_day` and `expected_amount_cents`), follow-ups (`next_action_on`). | `core.events`, `core.tasks`, `budget.accounts`, `jobs.leads` |
| **Waiting / stuck** | Items waiting on someone, and anything logged `stuck` more than once. | `core.tasks`, `core.log_entries` |
| **Ask** | A single input (the command palette in "ask" mode) for quick questions. | See below |

Energy and context are set with one keypress at the top ("low / med / high", "laptop / tablet / audio / focus"). Default to the last used.

### Quick answers

Questions I want answered in seconds. Each has a saved view first; AI handles the variations.

| Question | Mechanism |
| --- | --- |
| History of a budget item (the power bill) | `v_account_history(account)`: dated amounts, average, last change. Palette: `bill power`. |
| Will I go negative this month? | `v_cashflow`: balance plus upcoming bills and income by date, with the low point. |
| What did I do this week? | `v_week_summary`: log entries and time by goal. Optional AI paragraph. |
| What are my goals this week? | This week's priority, open milestones, and the tasks feeding them. |
| Where is this job lead? | `jobs.leads` by company, with next action and date. |
| What was I doing on this project? | Last log entries and last timer entry for the project. |

## AI layer

Four layers, built in this order. Details in [[11 Open Questions]].

1. **Read-only SQL role** (exists today via postgres-mcp in Raycast): works as soon as the data is in Postgres.
2. **Alfred MCP server:** a small local process with named tools. Works when the app is closed. Read tools first (`get_today`, `week_summary`, `search_tasks`, `account_history`, `list_leads`, `search_documents`), then confirmed writes (`add_task`, `complete_task`, `log_entry`).
3. **Markdown exports:** generated `today.md`, `week.md`, `status.md` in `Alfred/_export/`. Replaces hand-pasting `05 Current Status` into Claude Projects. Never edited by hand.
4. **In-app AI:** the palette's Ask mode, a daily brief, capture triage, the job-fit screener, and the weekly review. Uses the same tools as layer 2. Key in the macOS Keychain. Sonnet by default; Opus for reviews, per [[AI Instructions]].

Guardrails: AI never gets write SQL. AI writes go through named tools, show what will change, and are logged with `source: ai`. No vector database until a real question fails with full-text search plus SQL.

## UI and theme

- **Zones:** titlebar overlay, sidebar, main, inspector, status bar. Views swap inside main.
- **Tokens:** `tokens.css` holds every color, size, and font. Light first: Catppuccin Latte values (`--base #eff1f5`, `--mantle #e6e9ef`, `--crust #dce0e8`, `--text #4c4f69`, `--blue #1e66f5`) under the same variable names as Mocha. Dark later is `[data-theme="dark"]` with the Mocha values. No hard-coded colors in components.
- **Type and density:** base 14px, a zoom control (Cmd+Plus and Cmd+Minus), compact and comfortable density.
- **Primitives first:** Button, Input, ListRow, Card, Panel, Kbd, EmptyState, CommandPalette. Screens are assembled from these only. Rules for AI builders are in the app's `AGENTS.md`.
- **Headless pieces:** for dialogs, popovers, comboboxes, and context menus, use an accessible headless library and style it with tokens, instead of hand-building focus handling.

## MVP slices

Each slice is one increment. A slice is finished only when its **Done when** checks pass and earlier slices still work. Appetite: two weeks each at most. If a slice runs over, cut scope, not the date.

### 1. Foundation

**Done when**
- The shell renders the zones with light tokens, and the window shows after first paint.
- The app connects to the `alfred` database, runs numbered migrations, and shows the migration version in the status bar.
- Cmd+K opens a palette that lists views and commands.

### 2. Tasks

**Done when**
- `08 Next Steps` is imported once, and the note carries the frozen banner.
- I can add, edit, complete, and delete a task from the keyboard.
- Filters by energy, context, time, and goal work.
- A task added from the CLI appears in the app without a restart.

### 3. Today

**Done when**
- The Today view shows Now, This week, Done, and Upcoming from real data.
- Setting energy and context changes the recommended task.
- It is the first view after launch and after the global hotkey.

### 4. Log and timer

**Done when**
- `10 Log/Progress` is imported once and frozen.
- Completing a task writes a `done` entry. Decisions, wins, and stuck entries have a quick-add.
- One task can be current, and its elapsed time is visible.
- `v_week_summary` powers a Week view.

### 5. AI access

**Done when**
- The MCP server runs without the app, and Cursor and Raycast both list its tools.
- `get_today`, `week_summary`, and `search_tasks` return correct data.
- `add_task` and `log_entry` work from Raycast and create rows with `source: ai`.
- `_export/today.md` and `_export/week.md` regenerate when data changes.

### 6. Budget

**Done when**
- Existing budget data lives in the `budget` schema.
- Account history works from the palette (`bill power`).
- Upcoming bills show on Today, and `v_cashflow` shows the low point for the month.

### 7. Job Hunt

**Done when**
- Pasting a posting runs the fit screener and saves a lead with the JSON result.
- Leads move through stages with a next action and date.
- Follow-ups due appear on Today.

## Later

Start these only after the slices above are reliable.

- Calendar view (month and week), using a library behind an adapter. Until then, Upcoming is a list.
- Data grid for budget transactions, only if a plain table stops being enough.
- Board view of tasks (Todo, In Progress, Done), with a work-in-progress limit.
- Menu bar timer and quick capture without opening the main window.
- Health log module.
- Apple Calendar (read-only) and Raycast extension.
- Dark theme.
- Weekly review as a guided flow with an AI-written summary.
- Freelance module, after the first paying client.

## Development

Prerequisites: Node.js 20+, Rust stable, Xcode Command Line Tools, local PostgreSQL 18.

```sh
cd "/Users/greg/Code/Local Apps/alfred"
npm install
npm run tauri-dev
```

Frontend only:

```sh
npm run dev
```

Release build:

```sh
npm run tauri-build
```

Use a separate `alfred_dev` database with seed data for development. Production data is the `alfred` database.

## Scope

One person, one Mac. Team features, hosted accounts, sync, and mobile are out of scope until the local loop is dependable.
