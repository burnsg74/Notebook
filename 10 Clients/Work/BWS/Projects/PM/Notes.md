---
note_type: Project
created: 2026-09-28 17:38
updated: 2026-10-24
---
# Master App (name to be decided)

A fast, local-first task board and "what next" tool for one person, formerly planned as the PM app.

The goal is to keep the useful parts of Jira, monday.com, and ClickUp (tasks, status, priorities, planning, and time tracking) without their latency, complexity, or dependence on a browser, and to add what they lack: telling me what to do next for my energy and context, and showing a history of what got done. The experience should feel closer to using Zed: focused, responsive, keyboard-driven, and easy to customize.

Goals, decisions, and the other planned apps are in [[07 Tool Ideas]]. Whether this becomes one app with sections (job hunt, budget, freelance) or stays one of several small apps is still open.

> This project is an early prototype. Build one slice at a time. Leave the app usable at the end of each slice before starting the next one.

## Product principles

- **Notes are the data:** the app reads and writes the markdown notes in `10 Projects/Personal System`. There is no app database holding a second copy.
- **Local-first:** core features work without an account or internet connection.
- **Fast by default:** instant startup, navigation, search, and updates.
- **Keyboard-first:** every common action has a shortcut or command.
- **Workflow-driven:** adapt the app to the user instead of forcing a rigid process.
- **Focused:** make the current task and next action obvious.
- **Interoperable:** integrate with existing tools when that saves time; do not recreate them without a clear benefit.
- **Private:** data stays on the device (and in Dropbox, where the vault already syncs) unless the user explicitly enables another integration.

## How to build

Work through the slices below in order. Each slice is one increment for a person or an AI session.

- Implement only the current slice.
- Keep the decisions in [MVP decisions](#mvp-decisions) fixed until slice 7 is done.
- Treat [Later](#later) as out of scope for the current slice.
- A slice is finished only when its **Done when** checks pass and earlier slices still work.

## Data

The vault folder is a setting, defaulting to `~/Library/CloudStorage/Dropbox/Notebooks/Work/10 Projects/Personal System`.

| File | App use | Format |
| --- | --- | --- |
| `08 Next Steps.md` | Tasks: read, create, edit, reorder, complete | Task lines, `format: v1` |
| `10 Log/Progress.md` | Append `done` and `time` lines | Log lines, `format: v1` |
| `05 Current Status.md` | Read: this week's one priority (later) | Prose |
| `09 Roadmap.md` | Read: milestones (later) | Milestone lines, `format: v1` |

Formats are defined at the top of each note. Mapping from the board to task lines:

| Board | Task line |
| --- | --- |
| Ticket | One `- [ ]` or `- [x]` line under a section in `08 Next Steps` |
| Title | Text before the first `|` |
| Todo | `- [ ]` with no `status` field |
| In Progress | `- [ ]` with `status: doing` |
| Done | `- [x]`; the app also appends a `done` line to `10 Log/Progress` |
| ID | `id:` field, added by the app the first time it writes a line |
| Description | `note:` field (one line); longer text goes in a note named in `link:` |
| Order | Line order within its section |
| Time entry | `time` line in `10 Log/Progress` with `duration:` and the task `id:` |

Rules for writing files:

- Change only the lines the user edited. Never reformat other lines, and keep fields the app doesn't know about.
- Write atomically (temp file, then rename) so Obsidian and Dropbox never see half a file.
- Watch the files and reload when they change outside the app (Obsidian, Cursor, another device).
- Lines the app can't parse are shown as-is and left untouched.
- Only UI state that isn't plan data (window layout, the currently running timer) lives in the app's support folder.

## MVP decisions

These choices stay fixed through the first usable release:

- One local user. Data is the markdown notes above.
- Three statuses: Todo, In Progress, and Done, mapped as in [Data](#data).
- The vertical board is the main screen.
- The timer works inside the app before it moves to the menu bar.

## MVP slices

### 1. Read and create tasks

Show the tasks from `08 Next Steps` in a list, and create a title-only task.

**Done when**

- Existing task lines appear in the list, grouped by section.
- A new task is appended under the right section as a valid task line.
- Quit and reopen, and the tasks remain (because they're in the file).
- Editing the file in Obsidian updates the list.

### 2. Vertical board

Replace the list with the stacked board described in [Vertical Kanban](#vertical-kanban).

**Done when**

- Todo, In Progress, and Done are all visible on one screen.
- Todo and Done stay compact and can be expanded or collapsed.
- In Progress uses the remaining space.
- Moving a task between statuses updates its line, and completing one appends a `done` line to the progress log.

### 3. Task detail

Select a task and edit it from a detail panel.

**Done when**

- Selecting a task shows its title and fields (type, energy, time, context, goal, note, link).
- Edits are written back to that task's line only.
- `link:` opens the named note in Obsidian.

### 4. Filters

Add a sidebar to filter by `goal` and `type`.

**Done when**

- Selecting a goal or type shows only matching tasks.
- Filters survive a restart.

### 5. Order and basic shortcuts

Reorder work inside a section and move through the board from the keyboard.

**Done when**

- Reordering moves the line within its section in the file.
- A shortcut creates a task.
- Shortcuts move the selected task between statuses.
- The available shortcuts are visible in the app.

### 6. In-app current task

Track time against one current task from inside the app.

**Done when**

- One task can be marked current.
- Elapsed time is visible in the app.
- Switching tasks stops the previous timer and appends a `time` line to the progress log.
- Totals per task are computed from the progress log.

### 7. Menu bar timer

Show the current task in the macOS menu bar. The menu bar controls the timer; it does not duplicate the board.

**Done when**

- The menu bar shows the current task title and elapsed time.
- Pause, resume, and complete work from the menu bar.
- Switch among recent tasks from the menu bar.
- These actions work while the main window is hidden.
- Timer state still matches the app after a restart.

After slice 7, the MVP is a board over my own notes, with keyboard movement and a current-task timer.

## Vertical Kanban

The board stacks three workflow levels from top to bottom:

1. **Todo**
2. **In Progress**
3. **Done**

In Progress is the primary working area. Todo and Done use compact, screen-limited sections so all three levels remain visible without horizontal scrolling. Each compact section shows an item count and a useful task preview, and can be expanded or collapsed. Tasks move between levels with controls, drag and drop, or keyboard shortcuts as those interactions land in their slices.

## Experience and design

The visual direction is inspired by Zed's desktop experience:

- Dense but readable layouts with minimal visual noise.
- A filter sidebar, central board, and contextual detail panel.
- Fast transitions with no decorative animation that delays interaction.
- Clear typography, restrained color, and strong focus states.
- Dark and light themes, with configurable accent colors and density.
- Discoverable shortcut hints.

Accessibility remains a requirement: keyboard focus, contrast, reduced motion, and screen-reader semantics should stay intact as the layout gets denser.

Build the sidebar in slice 4 and the detail panel in slice 3. Themes, tabs, and a command palette wait until [Later](#later).

## Later

Start these only after the MVP slices are reliable.

### What next (first after the MVP)

- Pick for me: choose energy, context, and time available; show the two or three best-matching tasks, favoring this week's one priority from `05 Current Status`.
- Recurring tasks: show `recurring` tasks when they're due by their `every:` field, and log each completion.
- Progress view: what got done this week and month, grouped by goal, from `10 Log/Progress`.
- Roadmap view: open milestones from `09 Roadmap` and whether any tasks feed them.
- Quick health log entry, appended to `10 Log/Health`.

### Custom workflow

- Priority and due date fields.
- Subtasks and links to local files or external resources.
- Search, filters, and saved views.
- Custom statuses.
- Command palette.
- Editable shortcuts, including an optional Vim-style mode.
- Themes, density, tabs, and saved workspaces.
- Templates and lightweight time summaries.

### Keyboard workflow

The MVP ships only the shortcuts in slice 5. Later keyboard work includes:

- Quick capture from anywhere in the app.
- Task and recent-item switchers.
- Command palette with fuzzy matching.
- Shortcuts for completion and timer control.
- Multi-select and bulk editing.
- User-editable keybindings with conflict detection.

### Menu bar extras

Slice 7 covers the current task, elapsed time, and timer controls. Later menu bar work includes:

- Quick capture without opening the main window.
- A shortcut to reveal the current task in the app.
- Optional reminders when a timer runs too long or no task is active.

### Integrations

Add an integration only when it shortens a real workflow:

- **Raycast:** capture tasks, search tasks, switch the current task, and control the timer.
- **Obsidian:** open linked notes; Obsidian edits the same files the app does.
- **Cursor and Zed:** open a repository, file, or workspace attached to a task.
- **Warp:** open a project directory, launch a saved workflow, or run an approved command.
- **Git providers:** connect branches, commits, pull requests, and issues to tasks.
- **Calendars:** show due dates and time blocks without making calendar access a requirement.

Integrations stay optional, permission-scoped, and replaceable. The markdown notes remain the source of truth.

## Technical direction

The desktop app uses:

- [Tauri 2](https://tauri.app/) for the native application shell.
- [Svelte 5](https://svelte.dev/) for the interface.
- [Vite](https://vite.dev/) for development and builds.
- Rust for native capabilities: reading, writing, and watching the note files.

Implementation priorities:

- A line parser and writer for each note format, tested against real lines, including ones it doesn't understand.
- Format changes follow the note's `format:` version; the app refuses to write a file whose version it doesn't know.
- Native menu-bar support in slice 7.
- Background work kept small, observable, and power-efficient.
- Tests for parsing, writing, ordering, timers, and shortcuts in the slice that adds each behavior.

Indexed search, global shortcuts, and integrations belong to [Later](#later).

## Development

Prerequisites:

- Node.js
- [pnpm](https://pnpm.io/)
- The platform dependencies required by [Tauri](https://v2.tauri.app/start/prerequisites/)

Install dependencies and start the desktop app:

```sh
pnpm install
pnpm tauri-dev
```

Run only the web interface:

```sh
pnpm dev
```

Create a production desktop build:

```sh
pnpm tauri-build
```

For development, point the vault-folder setting at a copy of the notes, not the real ones.

## Scope

The MVP is one person using the app locally. Team collaboration, hosted accounts, real-time multiplayer, and enterprise administration stay out of scope until the local loop is dependable.

## License

MIT
