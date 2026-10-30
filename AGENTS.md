# Agent instructions

This vault holds the documents and notes behind Alfred, my personal assistant app. Markdown here is the record for documents. Alfred's database is the record for tasks, log, budget, and job leads. Cursor and Raycast are clients of both; they are not a second source of truth.

## Read first

Before changing the vault, read `20 Notes/Alfred/00 Start Here.md`. It maps the Alfred notes and says which to read for a given task. The app's project plan is `10 Clients/Work/BWS/Projects/Alfred/Notes.md`.

## How to work

1. Work on what the user asks. For planning, pick from `08 Next Steps` and check `05 Current Status`.
2. When something is finished or decided, add a line to `20 Notes/Alfred/10 Log/Progress.md` in its existing format. Decisions include a `why:` field. After Alfred's Log module ships, use Alfred's tools instead and leave that note frozen.
3. Notes with a `format:` field are import sources for Alfred. Keep new lines in that exact layout until the note carries a `Frozen:` banner, then stop editing it.
4. One project, one folder. Design, plan, done, and left stay together.

## Where notes go

- `00 Inbox` — unsorted capture. New captures use `Templates/Inbox.md` (`note_type: inbox`).
- `10 Projects` — work with an outcome. Open that folder's project note first.
- `20 Notes` — evergreen notes of every kind. Classify with `note_type` in frontmatter (`Note`, `Learning`, `How To`, `Research`, `Service`, `Reference`). Pick the matching template under `Templates/`. For `Note`, start the body with a one-line `>` description.
- `90 Archive` — finished or dropped.
- `Templates` — note templates; each sets `note_type`.

## Leave alone until Alfred decides it

Do not add learning subjects or a custom automation layer. App scope and build order live in `20 Notes/Alfred/07 Tool Ideas.md`; do not start a module it hasn't scheduled. A `note_type: Note` file is reference, not a learning subject: do not add level or practice tracking to it.
