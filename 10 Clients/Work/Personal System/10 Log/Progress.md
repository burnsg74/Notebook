---
note_type: Log
topic: Progress
created: 2026-10-24 07:45
format: v1
---
> Record of what got done, decided, and learned. One line per entry, newest at the bottom. This is the evidence of progress; the AI and the Master app read it for weekly reviews and to show how far I've come.

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
| `time` | Time spent on a task, usually written by the Master app's timer (`duration:` and `id:` fields) |

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
- 2026-10-24 | decision | Moved the Action Plan to 10 Projects/Personal System and pointed AGENTS.md and the Cursor vault rule at 00 Start Here | goal: systems | why: it's a live system and app data, not research; the rules pointed at a note that didn't exist
- 2026-10-24 | done | Prefilled About Me and Goals from job-hunt notes (schedule, household, money floor, target role) | goal: systems | link: 01 About Me
- 2026-10-24 | done | Rewrote the PM app plan as the Master app, reading and writing these notes instead of SQLite | goal: apps | link: 07 Tool Ideas
