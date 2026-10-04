---
status: active
type: project
---

# Personal System

Living note for this vault. In a later chat, read this file before changing the vault.

## How to resume

1. Read this note first.
2. Stay inside the current item under Next. Do not start Later work in the same slice.
3. When a slice is finished, add a line under Done, move that item out of Next, and write the following slice under Next.
4. If a working decision changes, edit Working decisions and say why under Done.

## Purpose

A personal knowledge system, PDA, and tracker in Obsidian markdown. Inspired by Getting Things Done, and by using Cursor and Raycast on the same notes. Built one small slice at a time.

## Current slice

Agent instructions are in place. No learning subjects, no project-management app, and no custom automation layer.

## Working decisions

- Markdown is the record for decisions, project memory, notes, and learning. A future project app, if one is chosen, holds dated commitments and shared status. Until then, next actions live in the project note.
- Cursor and Raycast are clients of this vault. They read and write these files. They are not a second database.
- Agent instructions live in `AGENTS.md` at the vault root, with a short always-on Cursor rule in `.cursor/rules/vault.mdc` that points at it. That is the AI integration. No custom automation layer.
- One project, one folder. Design, plan, done, and left stay together.
- Numbered top-level folders keep capture-to-archive order: `00 Inbox`, `10 Projects`, `20 Notes`, `90 Archive`, plus `Templates`.
- `20 Notes` holds one reference note per tool or topic, from `Templates/Note.md`, with a one-line `>` description. These are not learning subjects.
- Inbox captures use `Templates/Inbox.md`. There is no daily-note template right now.

## Open decisions

- What stays in markdown, and what would move to a project-management app once one is chosen.
- How Cursor and Raycast should capture or query notes, beyond reading `AGENTS.md` and this project note.
- How learning level and practice get tracked. Subjects in mind: AI, Cursor, Raycast, Obsidian, TypeScript, JavaScript. Each subject will need a level, recent practice, and what to drill. Not designed yet.

## Done

- 2026-10-03 — First slice: Home, inbox, project folders, this note, and a daily-note template. Daily notes and the template folder are set in Obsidian config.
- 2026-10-03 — Added `AGENTS.md` and `.cursor/rules/vault.mdc` so agents read vault rules before editing. No custom automation layer.
- 2026-10-04 — Added `20 Notes` with reference notes for Obsidian, Cursor IDE, Zed IDE, Warp, Raycast, Chrome, Mail, AI, and MacOS. Changed the folder decision: `20 Areas` and `30 Resources` were never created, and `20 Notes` replaced them because the vault actually uses it. Replaced the daily template with `Templates/Inbox.md` and `Templates/Note.md`, so the template decision now names those. Updated `AGENTS.md` to match. Cleared the daily-notes template setting, which pointed at the deleted `Templates/Daily`, and fixed the Home inbox query to filter on `note_type`.

## Next

Pick one open decision and design only that. Recommended first pick: where a learning subject note lives (a new folder, or a section inside its `20 Notes` note) and which three fields it must have (level, recent practice, what to drill). Do not build the subjects in that slice unless the decision is already made.

## Later

- Learning area for AI, Cursor, Raycast, Obsidian, TypeScript, and JavaScript.
- A dashboard query (Obsidian Bases) once there are enough typed notes to make it useful.
- A chosen project-management app, and the boundary with this vault.
- Cursor and Raycast workflows on top of these files.
