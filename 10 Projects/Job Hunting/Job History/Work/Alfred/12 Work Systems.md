---
note_type: Research
topic: Alfred
created: 2026-10-26
updated: 2026-10-26
status: draft
---
> Established systems worth borrowing, and the working system for each kind of task. Alfred holds these as configuration (statuses, fields, recurring checklists, Dashboard cards), not hard-coded behavior. AI: suggest changes to a system only after it has run for two weeks.

## How to use this note

A system is **entities** (what I track), **states** (stages), **a ritual** (the recurring habit), and **a view** (where I see it). Borrow one rule at a time. If a rule needs willpower on a tired day, shrink it.

## Established systems and what to take from each

| System | Core idea | Take | Leave |
| --- | --- | --- | --- |
| **GTD** (David Allen) | Capture everything, clarify each item into a next action, organize by context, review weekly | Inbox, "next action" wording, contexts (already in use), Waiting For, Someday/Maybe, weekly review | Full 5-stage ceremony, 30+ lists |
| **Today's Three** (Ivy Lee, 1-3-5, Big 3) | Pick a few tasks for the day, finish in order | Today shows at most three | Rigid time blocks |
| **Personal Kanban** (Jim Benson) | Visualize work and limit work in progress | A WIP limit of one or two "doing" tasks | Elaborate boards |
| **Shape Up** (Basecamp) | Fix the time (appetite), flex the scope. A circuit breaker stops runaway projects | Two-week appetite per Alfred slice; cut scope, not the date | Teams, pitches, betting tables |
| **PARA** (Tiago Forte) | Projects (outcomes), Areas (ongoing), Resources, Archive | Every task belongs to a project or an area | Reorganizing the whole vault |
| **Weekly review and retrospective** | Look back, look ahead, adjust | 15-minute checklist below; "start, stop, continue" | Long reflection |
| **Sales pipeline** | Stages with a next action and date for every lead | Job Hunt stages and follow-up dates | CRM features |
| **Bill calendar and cash-flow forecast** | Know what is due when, and the lowest balance this month | Upcoming bills on Today, the monthly low point | Envelopes, categories for everything |
| **Deliberate practice and spaced repetition** | Practice the weak spot; revisit on a schedule | One learning project, short repeat sessions, retrieval questions | Collecting courses |
| **Timeboxing / Pomodoro** | A fixed short work block | A 25-minute timer for low-energy starts | Counting sessions |

## Systems to start with

1. Capture
2. Next action (energy, time, context)
3. Today's Three
4. Weekly review
5. Log

Everything below is for later or per task type.

## Daily rhythm

| When | Do | Time |
| --- | --- | --- |
| Start | Open Today. Set energy and context. Pick from Now. | 2 min |
| During | Work one task at a time. Capture stray thoughts to the inbox, don't act on them. | |
| End | Log what got done. Note the next step for anything half done. | 3 min |

## Weekly review

About 15 minutes, Sunday, laptop. On a bad day do only the first three.

- [ ] Empty the inbox: each item becomes a task, a note, or gets deleted.
- [ ] Look at this week's log. Check off what is true.
- [ ] Pick next week's one priority.
- [ ] Read stuck items. For each: shrink, drop, or ask for help.
- [ ] Check upcoming bills and the cash-flow low point.
- [ ] Check job leads with a follow-up due.
- [ ] Look at the 3-month milestones. Any slipping?
- [ ] Update `05 Current Status` (until Alfred exports it).

## By type of task

### Programming (Alfred and client work)

| | |
| --- | --- |
| Entities | Project, task, session |
| States | Todo, Doing, Done (at most two Doing) |
| Rules | Two-week appetite per slice. A task is a next action that fits one sitting. "Done when" checks, written first. |
| Ritual | **Start:** read the last log line for the project (where I stopped, next step). **End:** write that line before closing the editor. Run the timer. |
| View | Project card on Today with last session and next step |
| Alfred | Task, timer, log, `repo_path` |

### Job Hunt

| | |
| --- | --- |
| Entities | Lead, application, contact, interview story |
| States | Lead, Screened, Applied, Replied, Interview, Offer, Closed (with reason) |
| Rules | Run every posting through the fit screener first. Apply only to close fits. Every open lead has a next action and a date. Ask about interview format before committing time. |
| Ritual | Daily 30-minute board sweep with one or two applications. Saturday interview practice. Sunday follow-ups. |
| View | Pipeline by stage, follow-ups due today, weekly counts (leads, applied, replies, interviews) |
| Alfred | `jobs.leads`, fit screener, recurring tasks |

### Budget

| | |
| --- | --- |
| Entities | Account, bill, transaction, import |
| States | Bill: due, paid, late. Month: open, reconciled. |
| Rules | Money in integer cents. Import statements once a week, same day. Every recurring bill has `due_day` and `expected_amount_cents`. |
| Ritual | **Weekly:** import and check. **Monthly:** compare actuals with expected, look at the low point. |
| View | Upcoming bills, projected low point, history per account |
| Alfred | `budget.*`, `v_cashflow`, `v_account_history` |

### Learning

| | |
| --- | --- |
| Entities | Learning project, session, question |
| States | Queue, Active (one at a time), Done |
| Rules | One active learning project, tied to a goal (for example Svelte 5, Tauri, Cursor workflows). Learn by building. End each session with two questions I could answer from memory next time. |
| Ritual | Three short sessions a week on high-energy days. Review last session's questions first. |
| View | The active project, last session, questions due |
| Alfred | Tasks of kind `learn`, log `win` entries |

### Interview practice

| | |
| --- | --- |
| Entities | Story, question, recording |
| States | Draft, Rehearsed, Told in a real interview |
| Rules | Five to seven stories, not memorized answers. Record on a phone, review once. |
| Ritual | Saturday, one story out loud, recorded |
| View | Stories by state, last practiced |
| Alfred | Tasks, documents (stories stay in markdown) |

### Life admin and health

| | |
| --- | --- |
| Entities | Recurring chore, appointment, health entry |
| Rules | A chore is a recurring task with a written way to do it. Health log lines are one short line, gaps are unknown, not good. |
| Ritual | Weekly review catches anything overdue |
| Alfred | Recurring tasks, events, later `health.entries` |

## Related

- Why and when: [[11 Open Questions]]
- App build: [[10 Clients/Work/BWS/Projects/Alfred/Notes|Alfred project plan]]
- Contexts and routines: [[06 Systems and Tools]]
