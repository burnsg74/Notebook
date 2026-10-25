---
note_type: Research
topic: Personal System
created: 2026-10-24 06:20
status: active
---
> Map of the Personal System files: what each one holds, which to give an AI for a given question, and how to keep them current.

## Purpose

These files are context for future AI chats and the data for my apps (see `07 Tool Ideas`). Each file answers one kind of question, so a chat gets only the files it needs instead of everything. Files with a `format:` field have a fixed line layout that apps parse; keep entries in that layout.

## Files

| File | Holds | Changes |
| --- | --- | --- |
| `00 Start Here` | This map | Rarely |
| `01 About Me` | Who I am, values, how I work best, limits (energy, health, time) | Rarely |
| `02 Goals` | Primary goals, why each matters, what "good enough" looks like | Quarterly |
| `03 Work History` | Career timeline, skills, wins, what I liked and disliked | Rarely |
| `04 Health` | Headaches and depression: history, triggers, what helped, current care | When something changes |
| `05 Current Status` | Dated snapshot: money, job search, health, active projects | Weekly |
| `06 Systems and Tools` | Everyday routines and the tools I use (Cursor, Zed, Raycast, Mail, Chrome, Obsidian, Warp, macOS) | When a system changes |
| `07 Tool Ideas` | Apps to build: Budget, Job Hunt, Freelance, Master app | When an idea changes |
| `08 Next Steps` | Menu of tasks tagged by type, energy, time, and context | Weekly |
| `09 Roadmap` | Milestones at 3, 6, and 12 months | Monthly |
| `10 Log/Progress` | Dated record of what got done or decided, as evidence of progress | Whenever |
| `10 Log/Health` | Dated entries for headaches, mood, meds, sleep, and triggers | Whenever |
| `AI Instructions` | How the AI should behave, which model fits which question, and how to get better answers | Rarely |

`10 Log` is a folder so the future Master app can read it. Entries use a fixed format so both people and apps can parse them.

## Which files to give the AI

Always include `AI Instructions` and `01 About Me`. Then add:

| Question type | Add these |
| --- | --- |
| "What should I do now?" | `05 Current Status`, `08 Next Steps` |
| Job search, resume, interview | `02 Goals`, `03 Work History`, `05 Current Status` |
| Money and budget | `02 Goals`, `05 Current Status` |
| Health, mood, headaches | `04 Health`, `10 Log/Health` (recent weeks) |
| Improve a tool or routine | `06 Systems and Tools` |
| Build a new app | `06 Systems and Tools`, `07 Tool Ideas` |
| Long-range planning | `02 Goals`, `05 Current Status`, `09 Roadmap`, `10 Log/Progress` |
| Weekly review | `05 Current Status`, `08 Next Steps`, `10 Log/Progress`, `10 Log/Health` |

## Upkeep

| When | Do |
| --- | --- |
| Whenever | Add lines to `10 Log/Health` and `10 Log/Progress`; check off tasks in `08 Next Steps` |
| Weekly, about 15 min | Refresh `05 Current Status`, pick next week's one priority, prune `08 Next Steps` |
| Monthly, about 20 min | Review `09 Roadmap` (steps are in that note) |
| When life changes | Update `01 About Me`, `02 Goals`, `04 Health`, or `06 Systems and Tools` |

Built 2026-10-24. History of changes is in `10 Log/Progress`.
