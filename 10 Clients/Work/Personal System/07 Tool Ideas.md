---
note_type: Research
topic: Personal System
created: 2026-10-24 07:25
updated: 2026-10-24
status: draft
---
> Apps I want to build to run my life with less effort: what each solves, the smallest useful version, what already exists, and build order. AI: help me keep each one small; push back if an idea grows before the last one is in use.

## Rules for building

- Build only what removes a real friction from `06 Systems and Tools` or moves a goal in `02 Goals`.
- Smallest useful version first, used for two weeks before adding more.
- One app in progress at a time.
- Local-first, on my Mac. Hosting setup is in [[App Setup Plan]].
- **These markdown files are the apps' data.** Apps read and write the notes in `10 Projects/Personal System` directly. No separate database holds a second copy. Any cache or index must be rebuildable from the notes.
- Every file an app reads keeps a stable, line-based format (like `10 Log/Health`). Change a format only by bumping its `format:` version.

## Open decisions

- **One app or several?** Either one app with sections (tasks, job hunt, budget, freelance) or separate small apps sharing these files. One app means one place to look and fits the Master app idea; separate apps are smaller to build and easier to drop. _Decide before building the Job Hunt app._
- **Name for the Master app.** _Ideas: Next, Compass, Home Base, Daybook, Helm, Steady._

## The apps

### Master app (formerly the PM app; name to be decided)

- **Problem:** I waste energy figuring out what to do next, which app to open, and whether I'm making progress. Jira, monday.com, and ClickUp are too slow and heavy for one person.
- **Does:** shows the next few tasks that fit my energy, time, and context; keyboard-first task board (Todo / In Progress / Done) with a current-task timer in the menu bar; opens the right app or note; records what got done.
- **Reads and writes:** `05 Current Status`, `08 Next Steps`, `10 Log`.
- **Smallest version:** no code. Open `08 Next Steps`, pick one task, add a line to `10 Log/Progress` when done. Build software only after this habit holds.
- **Exists:** full plan in 7 slices on Tauri 2 + Svelte 5, reading and writing these notes. [[10 Clients/Work/BWS/Projects/PM/Notes|Master app plan]]

### Job Hunt app

- **Problem:** finding close-fit remote jobs, screening out bad ones (and scams), tailoring applications, and tracking follow-ups takes more evening energy than I have.
- **Does:** collects postings, screens them against my profile and skill gaps, flags scam signs, drafts answers, tracks status and next action.
- **Exists:** CareerOS prompts: a job-fit screener that returns structured results, plus application-answer prompts. [[10 Clients/Work/Job Hunt 2/3-Profile/Skill gaps]] lists dealbreaker skills. Job boards are listed in `10 Clients/Work/Job Hunt/2-Job Boards`.
- **Smallest version:** _fill in (for example: paste a posting, get a fit score and red flags, save a note with status)_

### Budget app

- **Problem:** I need to know whether I'll go negative this month without working it out by hand.
- **Does:** bills and income against the $3,000/month floor; shows the gap or buffer.
- **Smallest version:** a spreadsheet or simple note, capped at about 2 hours, as [[Workflow Research]] suggested. Build an app only once the spreadsheet shows what's worth automating.
- **Exists:** local hosting guide (Next.js + Postgres) in [[App Setup Plan]].

### Freelance app

- **Problem:** tracking freelance leads, client work, hours, and invoices for Burns Web Services.
- **Exists:** business pitch in [[10 Clients/Work/BWS/Projects/Public Website/Notes|BWS Public Website]], [[Fiverr Seller Profile]].
- **Smallest version:** wait until there's a first paying client. Until then, a note per lead is enough.

## Suggested build order

To confirm or change.

1. **Master app, no-code version.** Use `08 Next Steps` and `10 Log/Progress` by hand for two weeks.
2. **Job Hunt app.** Most direct path to income and a remote job. Builds on CareerOS.
3. **Budget, spreadsheet version.** Two hours, done.
4. **Master app software,** once step 1 shows what the software must do.
5. **Freelance app,** after a first client.

## Ideas parking lot

New app ideas go here first, with one line on the problem. They don't get built until they move up into the list above.

- _fill in_
