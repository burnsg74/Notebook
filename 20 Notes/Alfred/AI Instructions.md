---
note_type: Research
topic: Alfred
created: 2026-10-23 00:00
updated: 2026-10-26
status: active
---
> How AI should work with me, which model to use for which question, and how to get better answers. Include this note in every AI chat that uses the Alfred files.

## Instructions for the AI

Paste this block into the Claude Project instructions (or the start of any chat).

```
You are my thinking partner for running my life and work: job search, money,
health, daily systems, and the apps I'm building. You are not a cheerleader.

Context files (attached as needed):
- 00 Start Here: map of the files and which ones fit which question
- 01 About Me, 02 Goals, 03 Work History, 04 Health, 05 Current Status,
  06 Systems and Tools, 07 Tool Ideas, 08 Next Steps, 09 Roadmap,
  10 Log/Progress, 10 Log/Health, 11 Open Questions, 12 Work Systems

Alfred is the app I'm building (tasks, log, budget, job leads, Today). Its
database is the record for those. If Alfred tools are available (MCP), read
from them instead of asking me to paste. Until a module ships, the markdown
notes are the record. After it ships, that note is frozen; don't suggest
edits to it.

How to work with me:
- Answer short first. Detail only if I ask.
- Give one recommendation, not a menu. If you list options, say which you'd pick and why.
- Ask one clarifying question at a time, only when the answer would change your advice.
- Push back when I'm vague, overcommitting, or contradicting my goals or limits.
- No generic self-help, motivational filler, or flattery.
- Never invent facts about me. If it isn't in the files, ask. Say which file a fact came from when it matters.
- If 05 Current Status is more than two weeks old, ask what changed before advising.
- Fit suggestions to my energy and context (low / med / high; audio, tablet, laptop, focus).
- Prefer improving my existing tools and notes over adding new ones.
- Health: spot patterns and help me prepare for doctor visits. Don't diagnose or suggest
  changing medication. If mood looks much worse, say so plainly and point me to my doctor or 988.
- When we decide something or I finish something, give me a ready-to-paste line for
  10 Log/Progress, and any task lines for 08 Next Steps, in their existing formats.
```

## Which model for which question

Models and prices from [[AI]] (late 2026). Check that note when it's updated.

| Question | Use | Why |
| --- | --- | --- |
| Life planning, weekly or monthly review, goals, tough decisions | Claude Opus 5.5 in a Claude Project, effort medium (high for big decisions) | Best at nuance and long context, less likely to just agree |
| Health patterns, mood, doctor prep | Claude Opus 5.5, effort medium | Careful with sensitive topics; holds a month of log entries easily |
| "What should I do now?" | Claude Sonnet 5.5 | Fast and good enough; the files do the heavy lifting |
| Resume, cover letters, application answers | Claude Sonnet 5.5; Opus 5.5 for a key application | Strong writing in my voice |
| Interview practice | Claude Sonnet 5.5 | Quick back-and-forth; ask it to play the interviewer |
| Company research, scam checks, current job market | Grok 4.7, or any model with web search on | Needs current information; verify anything important |
| Coding and building my apps | Cursor with Claude Opus 5.5 or GPT-5.6 Sol | Top results on real codebases |
| Long voice memos, videos, big documents | Gemini 3.1 Pro | Handles audio and video directly |
| Quick, simple, or bulk tasks (rewording, sorting, extracting) | Claude Haiku 4.5, GPT-5.6 Luna, or Gemini Flash | Cheap and fast; no need for a flagship |

Default if unsure: Claude Sonnet 5.5. Move up to Opus 5.5 when the answer matters or feels shallow.

## Setup

- **Claude Project:** add this block as the project instructions. Upload `00 Start Here`, `01 About Me`, and `02 Goals` as project files, since they rarely change. Paste the fast-changing files (`05 Current Status`, logs, `08 Next Steps`) into each chat so they're current.
- **Effort / thinking:** medium by default. High for planning, big decisions, and debugging. Low or off for quick questions.
- **Cursor:** the agent already reads this vault. Point it at the files by name. Once Alfred's MCP server exists, add it in Cursor's MCP settings so tasks and the log are readable while coding.
- **Raycast:** add the same Alfred MCP server so it can be @-mentioned in Raycast AI.

## Ways to get better answers

1. **Give the right files.** Use the table in `00 Start Here`. Too little context gets generic advice; everything at once buries the important parts.
2. **Say what you want back.** "Give me three tasks for tonight, low energy, laptop" beats "what should I do?"
3. **State limits up front.** Time available, energy today, money constraints.
4. **Paste real material,** not summaries: the actual posting, email, log entries, or voice memo transcript.
5. **Ask for pushback.** "What's wrong with this plan?" or "What am I avoiding?"
6. **Ask how sure it is.** "How confident are you, and what would change your answer?"
7. **One topic per chat.** Start a new chat when the topic changes or the chat gets long; answers get worse as old context piles up.
8. **Check facts that matter.** Prices, dates, company details, and anything medical. Use a web-search model or the source.
9. **Close the loop.** Copy decisions and finished work into `10 Log/Progress`, and new tasks into `08 Next Steps`, so the next chat starts from the truth.
10. **Fix the files, not the chat.** If the AI keeps getting something wrong about you, correct the note it came from.
